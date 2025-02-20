import React, { useState, useEffect } from "react";
import { getRewards } from "./api"; // Import the API function

const RewardsSelection = ({ categoryId }) => {
  const [rewards, setRewards] = useState([]);
  const [startDate, setStartDate] = useState("");
  const [endDate, setEndDate] = useState("");

  // Fetch rewards only if categoryId is 2
  const fetchRewards = async () => {
    if (categoryId !== 2 || !startDate || !endDate) return; // Exit if category is not 2 or dates are empty
    try {
      const response = await getRewards({ categoryId, startDate, endDate });
      setRewards(response.data);
    } catch (error) {
      console.error("Error fetching rewards:", error);
    }
  };

  useEffect(() => {
    fetchRewards();
  }, [categoryId, startDate, endDate]); // Fetch rewards when category and dates change

  return (
    <div>
      <h2>Travel Rewards</h2>

      {categoryId === 2 && (
        <div>
          {/* Date Filters */}
          <label>Start Date:</label>
          <input type="date" value={startDate} onChange={(e) => setStartDate(e.target.value)} />

          <label>End Date:</label>
          <input type="date" value={endDate} onChange={(e) => setEndDate(e.target.value)} />

          <button onClick={fetchRewards}>Apply Filters</button>

          {/* Rewards List */}
          <div>
            {rewards.length > 0 ? (
              rewards.map((reward) => (
                <div key={reward.id}>
                  <h3>{reward.name}</h3>
                  <p>{reward.description}</p>
                </div>
              ))
            ) : (
              <p>No rewards found</p>
            )}
          </div>
        </div>
      )}
    </div>
  );
};

export default RewardsSelection;