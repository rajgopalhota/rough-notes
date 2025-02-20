import React, { useEffect, useState } from "react";
import { Bar, Line, Pie } from "react-chartjs-2";
import { Chart, registerables } from "chart.js";
import { Container, Row, Col, Card, CardBody, CardTitle, Label, Input } from "reactstrap";

Chart.register(...registerables);

const TransactionsOverview = () => {
  const [transactions, setTransactions] = useState([]);
  const [selectedYear, setSelectedYear] = useState(new Date().getFullYear());

  useEffect(() => {
    const fetchTransactions = async () => {
      try {
        const response = await getAllTransactions(); // Replace with actual API call
        setTransactions(response || []);
      } catch (error) {
        console.error("Error fetching transactions:", error);
        setTransactions([]);
      }
    };
    fetchTransactions();
  }, []);

  // Filter processed transactions
  const processedTransactions = transactions.filter((t) => t.statusProcessed === "processed");
  const newTransactions = transactions.filter((t) => t.statusProcessed === "new");

  // Transactions Per Category
  const categoryCounts = processedTransactions.reduce((acc, { category }) => {
    acc[category] = (acc[category] || 0) + 1;
    return acc;
  }, {});

  // Processed Transactions Over the Year
  const processedMonthlyCounts = processedTransactions.reduce((acc, { creationDate }) => {
    const date = new Date(creationDate);
    if (date.getFullYear() === selectedYear) {
      const month = date.toLocaleString("default", { month: "short" });
      acc[month] = (acc[month] || 0) + 1;
    }
    return acc;
  }, {});

  // New Transactions Over the Year
  const newMonthlyCounts = newTransactions.reduce((acc, { creationDate }) => {
    const date = new Date(creationDate);
    if (date.getFullYear() === selectedYear) {
      const month = date.toLocaleString("default", { month: "short" });
      acc[month] = (acc[month] || 0) + 1;
    }
    return acc;
  }, {});

  // Top 10 Customers by Points Earned
  const customerPoints = processedTransactions.reduce((acc, { customerName, pointsEarned }) => {
    acc[customerName] = (acc[customerName] || 0) + pointsEarned;
    return acc;
  }, {});

  const topCustomers = Object.entries(customerPoints)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 10);

  const customerColors = [
    "#FF6384", "#36A2EB", "#FFCE56", "#4BC0C0", "#9966FF", "#FF9F40", "#C9CBCF", "#8C564B", "#E377C2", "#7F7F7F"
  ];

  return (
    <Container fluid>
      <h2 className="text-center my-4">Transactions Overview</h2>

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
        {/* Transactions Per Category */}
        <Col md={6}>
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Transactions by Category</CardTitle>
              <Bar
                data={{
                  labels: Object.keys(categoryCounts),
                  datasets: [{
                    label: "Number of Transactions",
                    data: Object.values(categoryCounts),
                    backgroundColor: "blue",
                  }]
                }}
                options={{ responsive: true }}
              />
            </CardBody>
          </Card>
        </Col>

        {/* Processed Transactions Over the Year */}
        <Col md={6}>
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Processed Transactions Over the Year</CardTitle>
              <Line
                data={{
                  labels: Object.keys(processedMonthlyCounts),
                  datasets: [{
                    label: "Processed Transactions per Month",
                    data: Object.values(processedMonthlyCounts),
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
              <CardTitle className="text-center">Top 10 Customers by Points Earned</CardTitle>
              <Pie
                data={{
                  labels: topCustomers.map((cust) => cust[0]),
                  datasets: [{
                    label: "Total Points Earned",
                    data: topCustomers.map((cust) => cust[1]),
                    backgroundColor: customerColors.slice(0, topCustomers.length)
                  }]
                }}
                options={{ responsive: true }}
              />
            </CardBody>
          </Card>
        </Col>

        {/* Processed vs New Transactions Over the Year */}
        <Col md={6} className="mt-4">
          <Card className="shadow">
            <CardBody>
              <CardTitle className="text-center">Processed vs New Transactions</CardTitle>
              <Line
                data={{
                  labels: Object.keys(processedMonthlyCounts),
                  datasets: [
                    {
                      label: "Processed Transactions",
                      data: Object.values(processedMonthlyCounts),
                      borderColor: "blue",
                      fill: false
                    },
                    {
                      label: "New Transactions",
                      data: Object.keys(processedMonthlyCounts).map((month) => newMonthlyCounts[month] || 0),
                      borderColor: "red",
                      fill: false
                    }
                  ]
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

export default TransactionsOverview;