import React, { useState } from "react";

export default function DateInput() {
  const [rawValue, setRawValue] = useState("");

  // Helper to return padded segment for day, month, year segments
  const getSegment = (value, length) => {
    const padded = (value + "").padEnd(length, " ");
    return padded;
  };

  const handleChange = (e) => {
    // Only allow digits, limit to 8 characters (DDMMYYYY)
    const cleaned = e.target.value.replace(/[^0-9]/g, "").slice(0, 8);
    setRawValue(cleaned);
  };

  // Split the raw value into segments: day (2), month (2), year (4)
  const dayDigits = rawValue.slice(0, 2);
  const monthDigits = rawValue.slice(2, 4);
  const yearDigits = rawValue.slice(4, 8);

  const formattedDay = getSegment(dayDigits, 2);
  const formattedMonth = getSegment(monthDigits, 2);
  const formattedYear = getSegment(yearDigits, 4);

  // Create a ghost string using flex layout to separate segments
  const ghostContent = (
    <>
      <span className="segment">{formattedDay}</span>
      <span className="slash">/</span>
      <span className="segment">{formattedMonth}</span>
      <span className="slash">/</span>
      <span className="segment">{formattedYear}</span>
    </>
  );

  return (
    <div className="date-input-wrapper">
      <input
        type="text"
        value={rawValue}
        onChange={handleChange}
        placeholder="DDMMYYYY"
        className="date-input"
      />
      {rawValue && <div className="ghost-overlay">{ghostContent}</div>}
      <style>{`
        .date-input-wrapper {
          position: relative;
          display: inline-block;
          font-family: monospace;
        }
        /* The actual input: text is transparent, caret remains visible */
        .date-input {
          position: relative;
          background: transparent;
          color: transparent;
          caret-color: black;
          border: 1px solid #ccc;
          padding: 8px;
          border-radius: 4px;
          width: 180px;
          letter-spacing: 0.1em;
          font-size: 16px;
        }
        /* The overlay displays the formatted text */
        .ghost-overlay {
          position: absolute;
          top: 0;
          left: 8px; /* match input padding */
          height: 100%;
          width: calc(100% - 16px);
          pointer-events: none;
          display: flex;
          align-items: center;
          font-size: 16px;
          letter-spacing: 0.1em;
          color: #aaa;
          background: transparent;
        }
        .ghost-overlay .segment {
          min-width: 20px;
          text-align: center;
        }
        .ghost-overlay .slash {
          margin: 0 4px;
        }
      `}</style>
    </div>
  );
}