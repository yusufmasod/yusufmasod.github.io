---
layout: default
title: Malaysia Printing Firms Directory
---

<link rel="stylesheet" href="/assets/css/custom.css">

# Licensed Printing Firms Directory (Malaysia)

# Licensed Printing Firms Directory (Malaysia)

This directory compiles companies associated with printing press licensing records administered by the Ministry of Home Affairs Malaysia under the Printing Presses and Publications Act 1984.

The list is presented as a reference dataset for academic, educational, and informational purposes. It provides a searchable directory of printing firms including company name, state, district, and address information.

**Source note**  
The information is compiled from publicly available licensing records and related regulatory materials issued by the Ministry of Home Affairs Malaysia.

**Disclaimer**  
This directory is not an official publication of the Ministry of Home Affairs. The information may not reflect the most recent licensing status, changes in company details, or updates made after the source records were published. (Last updated dataset: March 2026)

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css">
<script src="https://code.jquery.com/jquery-3.7.0.js"></script>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>

<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

<!-- Column resize plugin -->
<script src="https://cdn.jsdelivr.net/npm/datatables-colresize@1.0.2/jquery.dataTables.colResize.min.js"></script>

<style>
  .table-wrap {
    width: 100%;
    overflow-x: auto;
  }

  /* Keep headers readable */
  #directory th {
    white-space: nowrap;
  }

  /* Align rows nicely */
  #directory td {
    vertical-align: top;
  }

  /* Address column: shorten but show full text when hovered */
  #directory td:nth-child(4) {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 520px;
    position: relative;
  }

  #directory td:nth-child(4):hover {
    white-space: normal;
    overflow: visible;
    background: #ffffff;
    z-index: 10;
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
  const t = String(s).toLowerCase();
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
        let company  = getVal(r, ["Company", "SYARIKAT", "Syarikat", "Nama Syarikat", "NAMA SYARIKAT"]);
        let state    = getVal(r, ["State", "NEGERI", "Negeri"]);
        let district = getVal(r, ["District", "DAERAH", "Daerah"]);
        let address  = getVal(r, ["Address", "ALAMAT", "Alamat", "ADDRESS"]);
        let contact  = getVal(r, ["Contact", "KONTAK", "Telefon", "TEL", "No Telefon", "E-mel", "Email", "EMEL"]);
        let website  = getVal(r, ["Website", "LAMAN WEB", "Laman Web", "URL", "Web"]);
        let cert     = getVal(r, ["Industry Certification", "Certification", "Pensijilan", "PENSIJILAN"]);
        let sector   = getVal(r, ["Printing Sector Category", "Sector", "Kategori", "KATEGORI"]);
        let core     = getVal(r, ["Core Activities", "Activities", "Aktiviti", "AKTIVITI"]);

        // Fix for shifted extraction
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

      // Destroy existing table if the page reloads the script (prevents duplicates)
      if ($.fn.DataTable.isDataTable('#directory')) {
        $('#directory').DataTable().clear().destroy();
      }

      $('#directory').DataTable({
        data: tableData,
        pageLength: 25,
        autoWidth: false,
        deferRender: true,

        // Enable column resizing
        colResize: {
          resizeTable: true
        }
      });
    },

    error: function(err) {
      console.error("CSV load error:", err);
      alert("Could not load the CSV. Check the file name and ensure it is in the repo root.");
    }
  });

});
</script>
