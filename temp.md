import React, { useEffect, useState } from "react";
import { Button, ButtonGroup, Row, Col, FormGroup, Input, Label } from "reactstrap";
import "moneta-bootstrap/dist/css/moneta-bootstrap.min.css"; // Moneta styles

const RewardFilters = ({ getRewards }) => {
  const [items, setItems] = useState([]);
  const [allItems, setAllItems] = useState([]);
  const [showAll, setShowAll] = useState(false);
  const [filters, setFilters] = useState(["active", "inactive", "sale"]);

  useEffect(() => {
    const fetchRewards = async () => {
      const data = await getRewards();
      setAllItems(data);
      setItems(getRandomItems(data, 6));
    };
    fetchRewards();
  }, []);

  const getRandomItems = (data, count) => {
    let finalArray = [];
    while (finalArray.length < count && data.length > 0) {
      let item = data[Math.floor(Math.random() * data.length)];
      if (!finalArray.includes(item)) finalArray.push(item);
    }
    return finalArray;
  };

  const applyFilters = (items) => items.filter((item) => filters.includes(item.status));

  const displayedItems = showAll ? applyFilters(allItems) : applyFilters(items);

  const toggleShowAll = () => setShowAll((prev) => !prev);

  const handleFilterToggle = (filter) => {
    setFilters((prev) =>
      prev.includes(filter) ? prev.filter((f) => f !== filter) : [...prev, filter]
    );
  };

  return (
    <div className="container mt-4">
      {/* Toggle Between Random 6 & All */}
      <Row className="mb-3">
        <Col className="d-flex justify-content-center">
          <ButtonGroup>
            <Button
              color={showAll ? "outline-primary" : "primary"}
              className={`px-4 ${!showAll && "fw-bold"}`}
              onClick={() => setShowAll(false)}
            >
              Show Random 6
            </Button>
            <Button
              color={!showAll ? "outline-primary" : "primary"}
              className={`px-4 ${showAll && "fw-bold"}`}
              onClick={() => setShowAll(true)}
            >
              Show All
            </Button>
          </ButtonGroup>
        </Col>
      </Row>

      {/* Filter Toggle Switches */}
      <Row className="mb-3">
        <Col className="d-flex justify-content-center gap-4">
          {["active", "inactive", "sale"].map((filter) => (
            <FormGroup key={filter} switch className="d-flex align-items-center gap-2">
              <Label className="form-switch-label">{filter.charAt(0).toUpperCase() + filter.slice(1)}</Label>
              <Input
                type="switch"
                checked={filters.includes(filter)}
                onChange={() => handleFilterToggle(filter)}
                className="form-switch"
              />
            </FormGroup>
          ))}
        </Col>
      </Row>

      {/* Display Filtered Items */}
      <Row>
        {displayedItems.length ? (
          displayedItems.map((item) => (
            <Col key={item.id} md={4} className="mb-3">
              <div className="card p-3 shadow-sm border">
                <h5>{item.name}</h5>
                <p className="text-muted">{item.status}</p>
              </div>
            </Col>
          ))
        ) : (
          <Col>
            <p className="text-center text-danger">No items available</p>
          </Col>
        )}
      </Row>
    </div>
  );
};

export default RewardFilters;