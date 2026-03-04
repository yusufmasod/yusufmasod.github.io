---
layout: default
title: Malaysia Printing Firms Directory
---

<link rel="stylesheet" href="/assets/css/custom.css">

# Licensed Printing Firms Directory (Malaysia)

This page lists printing firms compiled from licensing records of the Ministry of Home Affairs Malaysia.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css">
<script src="https://code.jquery.com/jquery-3.7.0.js"></script>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

<style>
  .table-wrap { width: 100%; overflow-x: auto; }

  /* Keep headers readable */
  #directory th { white-space: nowrap; }

  /* Keep rows compact */
  #directory td { vertical-align: top; }

  /* Optional: make Address not bulldoze the layout */
  #directory td:nth-child(4) {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 520px;
  }
</style>

<div class="table-wrap">
  <table id="directory" class="display" style="width:100%">
    <thead>
      <tr>
        <th>Company</th>
        <th>State</th>
        <th>District</th>
        <th>Address</th>
        <th>Contact</th>
        <th>Website</th>
        <th>Industry Certification</th>
        <th>Printing Sector Category</th>
        <th>Core Activities</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>
</div>

<script>
function getVal(row, keys) {
  for (const k of keys) {
    if (row && Object.prototype.hasOwnProperty.call(row, k) && String(row[k]).trim() !== "") {
      return String(row[k]).trim();
    }
  }
  return "";
}

function looksLikeUrl(value) {
  if (!value) return false;
  const s = String(value).trim();
  if (!s) return false;
  const lower = s.toLowerCase();
  if (lower === "when available" || lower === "n/a" || lower === "-") return false;
  if (s.startsWith("http://") || s.startsWith("https://")) return true;
  return s.includes(".");
}

function makeClickableUrl(value) {
  if (!looksLikeUrl(value)) return "";
  const url = String(value).trim();
  const hasProtocol = url.startsWith("http://") || url.startsWith("https://");
  const safeUrl = hasProtocol ? url : "https://" + url;
  return `<a href="${safeUrl}" target="_blank" rel="noopener">${url}</a>`;
}

function looksLikeAddress(s) {
  if (!s) return false;
  // crude but useful: addresses usually contain digits + commas or "jalan/lorong/lot"
  const t = s.toLowerCase();
  return /\d/.test(t) && (t.includes(",") || t.includes("jalan") || t.includes("lorong") || t.includes("lot") || t.includes("taman"));
}

$(document).ready(function () {
  Papa.parse("printing_press_License%20directory.csv", {
    download: true,
    header: true,
    skipEmptyLines: true,

    complete: function(results) {
      const rows = results.data || [];

      const tableData = rows.map(r => {
        // Try multiple possible header names (PDF→CSV often changes them)
        let company = getVal(r, ["Company", "SYARIKAT", "Syarikat", "Nama Syarikat", "NAMA SYARIKAT"]);
        let state   = getVal(r, ["State", "NEGERI", "Negeri"]);
        let district= getVal(r, ["District", "DAERAH", "Daerah"]);
        let address = getVal(r, ["Address", "ALAMAT", "Alamat", "ADDRESS"]);
        let contact = getVal(r, ["Contact", "KONTAK", "Telefon", "TEL", "No Telefon", "E-mel", "Email", "EMEL"]);
        let website = getVal(r, ["Website", "LAMAN WEB", "Laman Web", "URL", "Web"]);
        let cert    = getVal(r, ["Industry Certification", "Certification", "Pensijilan", "PENSIJILAN"]);
        let sector  = getVal(r, ["Printing Sector Category", "Sector", "Kategori", "KATEGORI"]);
        let core    = getVal(r, ["Core Activities", "Activities", "Aktiviti", "AKTIVITI"]);

        // Heuristic fix for shifted extraction:
        // If district is empty, and address looks short (like a place name),
        // and contact looks like a full address, shift them right.
        if (!district && address && !looksLikeAddress(address) && looksLikeAddress(contact)) {
          district = address;
          address = contact;
          contact = "";
        }

        return [
          company,
          state,
          district,
          address,
          contact,
          makeClickableUrl(website),
          cert,
          sector,
          core
        ];
      });

      $('#directory').DataTable({
        data: tableData,
        pageLength: 25,
        autoWidth: false,
        deferRender: true
        // IMPORTANT: no scrollX — we use the .table-wrap horizontal scroll instead
      });
    },

    error: function(err) {
      console.error("CSV load error:", err);
      alert("Could not load the CSV. Check the file name and ensure it is in the repo root.");
    }
  });
});
</script>
