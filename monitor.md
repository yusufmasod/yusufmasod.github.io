# Malaysia Printing Industry Monitor

This page presents selected indicators on Malaysia’s printing industry based on official statistics (DOSM). It tracks changes in sales, employment, and wages over time.

<hr>

## Sales Trend

<canvas id="salesChart"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
const ctx = document.getElementById('salesChart').getContext('2d');

new Chart(ctx, {
  type: 'line',
  data: {
    labels: ['2019','2020','2021','2022','2023','2024','2025'],
    datasets: [{
      label: 'Sales Value (RM)',
      data: [100, 80, 85, 95, 110, 120, 130], // <-- replace later
      borderWidth: 2,
      fill: false
    }]
  },
  options: {
    responsive: true,
    plugins: {
      legend: {
        display: true
      }
    }
  }
});
</script>
