import React, { useEffect, useState } from "react";
import { Bar, Line, Pie } from "react-chartjs-2";
import { Chart, registerables } from "chart.js";
import { Container, Row, Col, Card, CardBody, CardTitle, Label, Input } from "reactstrap";

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

  // Top 10 Customers with Unique Colors
  const customerCounts = vendorFulfillments.reduce((acc, { customerName, fulfillmentQuantity }) => {
    acc[customerName] = (acc[customerName] || 0) + fulfillmentQuantity;
    return acc;
  }, {});

  const topCustomers = Object.entries(customerCounts)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 10);

  const customerColors = [
    "#FF6384", "#36A2EB", "#FFCE56", "#4BC0C0", "#9966FF", "#FF9F40", "#C9CBCF", "#8C564B", "#E377C2", "#7F7F7F"
  ];

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
    <Container fluid>
      <h2 className="text-center my-4">Vendor Fulfillment Overview</h2>

      {/* Year Selector */}
      <Row className="justify-content-center mb-4">
        <Col md={4}>
          <Label>Select Year:</Label>
          <Input type="select" onChange={(e) => setSelectedYear(Number(e.target.value))} value={selectedYear}>
            {[2023, 2024, 2025].map((year) => (
              <option key={year} value={year}>{year}</option>
            ))}
          </Input>
        </Col>
      </Row>

      {/* Charts Section */}
      <Row>
        {/* Top Fulfilled Items */}
        <Col md={6}>
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Top 10 Fulfilled Items</CardTitle>
              <Bar
                data={{
                  labels: topItems.map((item) => item[0]),
                  datasets: [{
                    label: "Fulfilled Quantity",
                    data: topItems.map((item) => item[1]),
                    backgroundColor: "blue",
                  }]
                }}
                options={{ responsive: true }}
              />
            </CardBody>
          </Card>
        </Col>

        {/* Fulfillments Over the Year */}
        <Col md={6}>
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Fulfillments Over the Year</CardTitle>
              <Line
                data={{
                  labels: Object.keys(monthlyCounts),
                  datasets: [{
                    label: "Fulfillments per Month",
                    data: Object.values(monthlyCounts),
                    borderColor: "green",
                    fill: false
                  }]
                }}
                options={{ responsive: true }}
              />
            </CardBody>
          </Card>
        </Col>

        {/* Top Customers */}
        <Col md={6} className="mt-4">
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Top 10 Customers</CardTitle>
              <Pie
                data={{
                  labels: topCustomers.map((cust) => cust[0]),
                  datasets: [{
                    label: "Total Fulfillments",
                    data: topCustomers.map((cust) => cust[1]),
                    backgroundColor: customerColors.slice(0, topCustomers.length)
                  }]
                }}
                options={{ responsive: true }}
              />
            </CardBody>
          </Card>
        </Col>

        {/* Fulfillments Per Vendor Over Time */}
        <Col md={6} className="mt-4">
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Fulfillments Per Vendor</CardTitle>
              <Line
                data={{
                  labels: Object.keys(monthlyCounts),
                  datasets: Object.entries(vendorTimeCounts).map(([vendor, values], index) => ({
                    label: vendor,
                    data: Object.keys(monthlyCounts).map((month) => values[month] || 0),
                    borderColor: `#${Math.floor(Math.random() * 16777215).toString(16)}`,
                    fill: false
                  }))
                }}
                options={{ responsive: true }}
              />
            </CardBody>
          </Card>
        </Col>
      </Row>
    </Container>
  );
};

export default VendorFulfillmentOverview;