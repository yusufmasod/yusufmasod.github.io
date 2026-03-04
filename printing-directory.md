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

  /* Prevent header wrapping */
  #directory th { white-space: nowrap; }

  /* Make sure scroll head + body use the same width */
  .dataTables_scrollHeadInner,
  .dataTables_scrollHeadInner table,
  .dataTables_scrollBody table {
    width: 1800px !important;   /* <-- key: same width for both header+body */
  }
</style>

<div class="table-wrap">
  <table id="directory" class="display nowrap" style="width:100%">
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

$(document).ready(function () {

  Papa.parse("printing_press_License%20directory.csv", {
    download: true,
    header: true,
    skipEmptyLines: true,

    complete: function(results) {
      const rows = results.data || [];

      const tableData = rows.map(r => ([
        r["Company"] || "",
        r["State"] || "",
        r["District"] || "",
        r["Address"] || "",
        r["Contact"] || "",
        makeClickableUrl(r["Website"] || ""),
        r["Industry Certification"] || "",
        r["Printing Sector Category"] || "",
        r["Core Activities"] || ""
      ]));

      const dt = $('#directory').DataTable({
        data: tableData,
        pageLength: 25,
        autoWidth: false,
        deferRender: true,

        scrollX: true,
        scrollCollapse: true,

        // lock widths so DataTables stops "guessing"
        columns: [
          { width: "260px" }, // Company
          { width: "160px" }, // State
          { width: "160px" }, // District
          { width: "520px" }, // Address
          { width: "160px" }, // Contact
          { width: "160px" }, // Website
          { width: "220px" }, // Industry Certification
          { width: "260px" }, // Printing Sector Category
          { width: "220px" }  // Core Activities
        ],

        initComplete: function () {
          // After DataTables builds the two-table scroll structure:
          dt.columns.adjust();

          // Manual scroll sync (fixes your exact “shifted columns” symptom)
          const $body = $('.dataTables_scrollBody');
          $body.off('scroll.dtfix').on('scroll.dtfix', function () {
            $('.dataTables_scrollHead').scrollLeft($body.scrollLeft());
          });
        }
      });

      // Extra alignment passes (fonts/layout settle after init)
      setTimeout(function () { dt.columns.adjust(); }, 300);
      setTimeout(function () { dt.columns.adjust(); }, 1200);

      // One more after everything loads (images/fonts)
      $(window).on('load', function () {
        dt.columns.adjust();
      });

      // Keep aligned on resize
      $(window).on('resize', function () {
        dt.columns.adjust();
      });
    },

    error: function(err) {
      console.error("CSV load error:", err);
      alert("Could not load the CSV. Check the file name and ensure it is in the repo root.");
    }
  });

});
</script>
