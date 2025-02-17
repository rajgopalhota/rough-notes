import React, { useEffect, useState } from "react";
import { Button, ButtonGroup, Row, Col, FormGroup, Input, Label } from "reactstrap";
import { getRewards } from "./api"; // Assume this fetches rewards
import "moneta-bootstrap/dist/css/moneta-bootstrap.min.css"; // Ensure Moneta styles are applied

const RewardFilters = () => {
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

  const applyFilters = (items) => {
    return items.filter(item => filters.includes(item.status));
  };

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
        <Col>
          <ButtonGroup>
            <Button color={showAll ? "primary" : "outline-primary"} onClick={toggleShowAll}>
              Show All
            </Button>
            <Button color={!showAll ? "primary" : "outline-primary"} onClick={toggleShowAll}>
              Show Random 6
            </Button>
          </ButtonGroup>
        </Col>
      </Row>

      {/* Filter Toggles */}
      <Row>
        <Col>
          <FormGroup className="d-flex justify-content-center">
            {["active", "inactive", "sale"].map((filter) => (
              <div key={filter} className="mx-3">
                <Label className="form-check-label">{filter.charAt(0).toUpperCase() + filter.slice(1)}</Label>
                <Input
                  type="switch"
                  checked={filters.includes(filter)}
                  onChange={() => handleFilterToggle(filter)}
                  className="form-check-input"
                />
              </div>
            ))}
          </FormGroup>
        </Col>
      </Row>

      {/* Display Filtered Items */}
      <Row>
        {displayedItems.length ? (
          displayedItems.map((item) => (
            <Col key={item.id} md={4} className="mb-3">
              <div className="card p-3 shadow-sm">
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