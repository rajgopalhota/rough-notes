{/* Year Selector */}
<Row className="justify-content-center mb-4">
  <Col md={4}>
    <Label>Select Year:</Label>
    <Input type="select" onChange={(e) => setSelectedYear(Number(e.target.value))} value={selectedYear}>
      {[...Array(2025 - 2012 + 1)].map((_, index) => {
        const year = 2012 + index;
        return <option key={year} value={year}>{year}</option>;
      })}
    </Input>
  </Col>
</Row>