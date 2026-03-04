---
layout: default
title: Printing Firms Directory
---

# Licensed Printing Firms Directory (Malaysia)

This page lists printing firms compiled from licensing records of the Ministry of Home Affairs Malaysia.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.6/css/jquery.dataTables.min.css">

<script src="https://code.jquery.com/jquery-3.7.0.js"></script>
<script src="https://cdn.datatables.net/1.13.6/js/jquery.dataTables.min.js"></script>

<table id="directory" class="display">
<thead>
<tr>
<th>Company</th>
<th>State</th>
<th>District</th>
<th>Website</th>
<th>Certification</th>
<th>Sector</th>
</tr>
</thead>

<tbody>

<tr>
<td>Example Printing Sdn Bhd</td>
<td>Selangor</td>
<td>Klang</td>
<td>example.com</td>
<td>ISO 9001</td>
<td>Offset</td>
</tr>

<tr>
<td>ABC Digital Print</td>
<td>Kuala Lumpur</td>
<td>Kuala Lumpur</td>
<td>abcprint.com</td>
<td>-</td>
<td>Digital</td>
</tr>

</tbody>
</table>

<script>
$(document).ready(function () {
$('#directory').DataTable();
});
</script>
