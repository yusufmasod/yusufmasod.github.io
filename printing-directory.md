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
  /* Make the page use more width even on narrow Jekyll themes */
  .page-content, .wrapper, main, .container { max-width: 1200px; }
  /* Force full-width table area inside the content block */
  .table-wrap { width: 100%; overflow-x: auto; }
  table.dataTable { width: 100% !important; }
  /* Prevent header cells from wrapping weirdly */
  #directory th { white-space: nowrap; }
</style>

<div class="table-wrap">
  <table id="directory" class="display nowrap">
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
  if (lower === "when available" || lower === "n/a" || lower === "-" ) return false;
  if (s.startsWith("http://") || s.startsWith("https://")) return true;
  return s.includes(".");
}

function makeClickableUrl(value) {
  if (!looksLikeUrl(value)) return "";
  let url = String(value).trim();
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
        scrollX: true,
        scrollCollapse: true,
        autoWidth: false,
        fixedHeader: false
      });

      /* Key fix: recalc column widths AFTER data loads and table renders */
setTimeout(function () {
  dt.columns.adjust().draw(false);
}, 200);

/* Second adjustment after layout fully settles */
setTimeout(function () {
  dt.columns.adjust().draw(false);
}, 800);
