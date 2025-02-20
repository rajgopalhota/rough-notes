import React, { useEffect, useState } from "react";
import { Bar, Line, Pie } from "react-chartjs-2";
import { Chart, registerables } from "chart.js";

Chart.register(...registerables);

const VendorFulfillmentOverview = () => {
  const [fulfillments, setFulfillments] = useState([]);
  const [selectedYear, setSelectedYear] = useState(new Date().getFullYear());

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await getAllFulfillments(); // Fetch data
        setFulfillments(response || []);
      } catch (error) {
        console.error("Error fetching data:", error);
        setFulfillments([]);
      }
    };
    fetchData();
  }, []);

  // Filter successful vendor fulfillments
  const vendorFulfillments = fulfillments.filter(
    (f) => f.fulfillmentStaus === "Success"
  );

  // Top 10 Fulfilled Items
  const itemCounts = vendorFulfillments.reduce((acc, { rewardCatalogItemName, fulfillmentQuantity }) => {
    acc[rewardCatalogItemName] = (acc[rewardCatalogItemName] || 0) + fulfillmentQuantity;
    return acc;
  }, {});

  const topItems = Object.entries(itemCounts)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 10);

  // Fulfillments Over the Year
  const monthlyCounts = vendorFulfillments.reduce((acc, { fulfilmentCreationDate }) => {
    const date = new Date(fulfilmentCreationDate);
    if (date.getFullYear() === selectedYear) {
      const month = date.toLocaleString("default", { month: "short" });
      acc[month] = (acc[month] || 0) + 1;
    }
    return acc;
  }, {});

  // Top 10 Customers
  const customerCounts = vendorFulfillments.reduce((acc, { customerName, fulfillmentQuantity }) => {
    acc[customerName] = (acc[customerName] || 0) + fulfillmentQuantity;
    return acc;
  }, {});

  const topCustomers = Object.entries(customerCounts)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 10);

  // Fulfillments Per Vendor Over Time
  const vendorTimeCounts = vendorFulfillments.reduce((acc, { rewardCatalogItemName, fulfilmentCreationDate }) => {
    const date = new Date(fulfilmentCreationDate);
    if (date.getFullYear() === selectedYear) {
      const month = date.toLocaleString("default", { month: "short" });
      if (!acc[rewardCatalogItemName]) acc[rewardCatalogItemName] = {};
      acc[rewardCatalogItemName][month] = (acc[rewardCatalogItemName][month] || 0) + 1;
    }
    return acc;
  }, {});

  return (
    <div>
      <h2>Vendor Fulfillment Overview</h2>
      <label>Select Year:</label>
      <select onChange={(e) => setSelectedYear(Number(e.target.value))} value={selectedYear}>
        {[2023, 2024, 2025].map((year) => (
          <option key={year} value={year}>
            {year}
          </option>
        ))}
      </select>

      <div style={{ display: "flex", flexWrap: "wrap", gap: "20px" }}>
        {/* Top Fulfilled Items */}
        <div style={{ width: "45%" }}>
          <Bar
            data={{
              labels: topItems.map((item) => item[0]),
              datasets: [{ label: "Fulfilled Quantity", data: topItems.map((item) => item[1]), backgroundColor: "blue" }]
            }}
          />
        </div>

        {/* Fulfillments Over the Year */}
        <div style={{ width: "45%" }}>
          <Line
            data={{
              labels: Object.keys(monthlyCounts),
              datasets: [{ label: "Fulfillments per Month", data: Object.values(monthlyCounts), borderColor: "green", fill: false }]
            }}
          />
        </div>

        {/* Top Customers */}
        <div style={{ width: "45%" }}>
          <Pie
            data={{
              labels: topCustomers.map((cust) => cust[0]),
              datasets: [{ label: "Total Fulfillments", data: topCustomers.map((cust) => cust[1]), backgroundColor: "red" }]
            }}
          />
        </div>

        {/* Fulfillments Per Vendor */}
        <div style={{ width: "45%" }}>
          <Line
            data={{
              labels: Object.keys(monthlyCounts),
              datasets: Object.entries(vendorTimeCounts).map(([vendor, values]) => ({
                label: vendor,
                data: Object.keys(monthlyCounts).map((month) => values[month] || 0),
                borderColor: `#${Math.floor(Math.random() * 16777215).toString(16)}`,
                fill: false
              }))
            }}
          />
        </div>
      </div>
    </div>
  );
};

export default VendorFulfillmentOverview;