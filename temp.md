import React, { useEffect, useState } from "react"; import { Bar, Line, Pie } from "react-chartjs-2"; import { Chart, registerables } from "chart.js";

Chart.register(...registerables);

const VendorFulfillmentOverview = ({ data }) => { const [topItems, setTopItems] = useState({}); const [fulfillmentsOverTime, setFulfillmentsOverTime] = useState({}); const [topCustomers, setTopCustomers] = useState({}); const [fulfillmentsPerVendor, setFulfillmentsPerVendor] = useState({});

useEffect(() => { if (!data || data.length === 0) return;

const filteredData = data.filter(item => item.fulfillmentStaus === "Success");

// Top 10 Fulfilled Items
const itemsMap = {};
filteredData.forEach(({ rewardCatalogItemName, fulfillmentQuantity }) => {
  itemsMap[rewardCatalogItemName] = (itemsMap[rewardCatalogItemName] || 0) + fulfillmentQuantity;
});
const topItemsData = Object.entries(itemsMap)
  .sort((a, b) => b[1] - a[1])
  .slice(0, 10);
setTopItems({
  labels: topItemsData.map(item => item[0]),
  datasets: [{ label: "Fulfilled Quantity", data: topItemsData.map(item => item[1]), backgroundColor: "blue" }]
});

// Fulfillments Over Time (by Month)
const monthlyMap = {};
filteredData.forEach(({ fulfilmentCreationDate }) => {
  const month = new Date(fulfilmentCreationDate).toLocaleString("default", { month: "short" });
  monthlyMap[month] = (monthlyMap[month] || 0) + 1;
});
setFulfillmentsOverTime({
  labels: Object.keys(monthlyMap),
  datasets: [{ label: "Fulfillments per Month", data: Object.values(monthlyMap), borderColor: "green", fill: false }]
});

// Top 10 Customers
const customerMap = {};
filteredData.forEach(({ customerName, fulfillmentQuantity }) => {
  customerMap[customerName] = (customerMap[customerName] || 0) + fulfillmentQuantity;
});
const topCustomersData = Object.entries(customerMap)
  .sort((a, b) => b[1] - a[1])
  .slice(0, 10);
setTopCustomers({
  labels: topCustomersData.map(item => item[0]),
  datasets: [{ label: "Total Fulfillments", data: topCustomersData.map(item => item[1]), backgroundColor: "red" }]
});

// Fulfillments Per Vendor Over Time
const vendorMap = {};
filteredData.forEach(({ rewardCatalogItemName, fulfilmentCreationDate }) => {
  const month = new Date(fulfilmentCreationDate).toLocaleString("default", { month: "short" });
  if (!vendorMap[rewardCatalogItemName]) vendorMap[rewardCatalogItemName] = {};
  vendorMap[rewardCatalogItemName][month] = (vendorMap[rewardCatalogItemName][month] || 0) + 1;
});
setFulfillmentsPerVendor({
  labels: Object.keys(monthlyMap),
  datasets: Object.entries(vendorMap).map(([vendor, values]) => ({
    label: vendor,
    data: Object.keys(monthlyMap).map(month => values[month] || 0),
    borderColor: `#${Math.floor(Math.random() * 16777215).toString(16)}`,
    fill: false
  }))
});

}, [data]);

return ( <div> <h2>Vendor Fulfillment Overview</h2> <div style={{ display: "flex", flexWrap: "wrap", gap: "20px" }}> <div style={{ width: "45%" }}><Bar data={topItems} /></div> <div style={{ width: "45%" }}><Line data={fulfillmentsOverTime} /></div> <div style={{ width: "45%" }}><Pie data={topCustomers} /></div> <div style={{ width: "45%" }}><Line data={fulfillmentsPerVendor} /></div> </div> </div> ); };

export default VendorFulfillmentOverview;






npm install react-chartjs-2 chart.js