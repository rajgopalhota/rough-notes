import React, { useState } from "react";

export default function DateInput() {
  const [rawValue, setRawValue] = useState("");

  const handleChange = (e) => {
    // Only allow digits and limit to 8 (DDMMYYYY)
    const cleaned = e.target.value.replace(/[^0-9]/g, "").slice(0, 8);
    setRawValue(cleaned);
  };

  const formatDisplay = (value) => {
    // Split into day, month, year parts with placeholders
    const digits = value.split("");
    const day = (digits[0] || "_") + (digits[1] || "_");
    const month = (digits[2] || "_") + (digits[3] || "_");
    const year = (digits[4] || "_") + (digits[5] || "_") + (digits[6] || "_") + (digits[7] || "_");
    return `${day}/${month}/${year}`;
  };

  return (
    <div className="date-input-container">
      {/* The underlying input holds only raw digits */}
      <input
        className="date-input"
        type="text"
        value={rawValue}
        onChange={handleChange}
      />
      {/* The overlay displays the formatted string */}
      <div className="date-overlay">{formatDisplay(rawValue)}</div>
      <style>{`
        .date-input-container {
          position: relative;
          display: inline-block;
        }
        /* Underlying input: transparent text, caret still visible */
        .date-input {
          position: relative;
          background: transparent;
          color: transparent;
          caret-color: black;
          border: 1px solid #ccc;
          padding: 8px;
          border-radius: 4px;
          width: 150px;
          font-family: monospace;
          letter-spacing: 0.3em;
        }
        /* Overlay: positioned over the input */
        .date-overlay {
          position: absolute;
          top: 0;
          left: 0;
          height: 100%;
          width: 100%;
          pointer-events: none;
          border: 1px solid transparent;
          padding: 8px;
          font-family: monospace;
          letter-spacing: 0.3em;
          text-align: center;
          line-height: 1.5;
          color: #333;
        }
      `}</style>
    </div>
  );
}