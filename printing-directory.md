---
layout: default
title: Printing Firms Directory
---

# Licensed Printing Firms Directory (Malaysia)

This page lists printing firms compiled from licensing records of the Ministry of Home Affairs Malaysia.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css">

<script src="https://code.jquery.com/jquery-3.7.0.js"></script>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>

<script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>

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

<script>
function makeClickableUrl(url) {
  if (!url) return "";
  url = String(url).trim();
  if (!url) return "";
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

      $('#directory').DataTable({
        data: tableData,
        pageLength: 25
      });
    },
    error: function(err) {
      console.error("CSV load error:", err);
      alert("Could not load the CSV. Check the file name and that it is uploaded to the repo root.");
    }
  });
});
</script>
