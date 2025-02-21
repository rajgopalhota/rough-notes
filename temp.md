import React, { useState } from "react";

export default function DateInput() {
  const [rawValue, setRawValue] = useState("");

  // Create a formatted string (DD/MM/YYYY) based on the digits entered
  const formatDisplay = (value) => {
    const digits = value.split("");
    const day = (digits[0] || " ") + (digits[1] || " ");
    const month = (digits[2] || " ") + (digits[3] || " ");
    const year =
      (digits[4] || " ") +
      (digits[5] || " ") +
      (digits[6] || " ") +
      (digits[7] || " ");
    return `${day}/${month}/${year}`;
  };

  const handleChange = (e) => {
    // Allow only digits, limit to 8 characters (DDMMYYYY)
    const cleaned = e.target.value.replace(/[^0-9]/g, "").slice(0, 8);
    setRawValue(cleaned);
  };

  // The ghost formatted string is only shown when the user has started typing.
  const ghostText = rawValue ? formatDisplay(rawValue) : "";

  // Wrapper style uses a CSS variable for the ghost text.
  const wrapperStyle = {
    position: "relative",
    display: "inline-block",
  };

  return (
    <div style={wrapperStyle} className="date-input-wrapper">
      {/* The actual input holds raw digits */}
      <input
        type="text"
        value={rawValue}
        onChange={handleChange}
        placeholder="DDMMYYYY"
        style={{
          border: "1px solid #ccc",
          padding: "8px",
          paddingLeft: "8px",
          borderRadius: "4px",
          width: "150px",
          fontFamily: "monospace",
          letterSpacing: "0.3em",
          position: "relative",
          backgroundColor: "transparent",
          zIndex: 2,
          color: "#000",
        }}
      />
      {/* A background overlay for ghost text */}
      {ghostText && (
        <div className="ghost-overlay" aria-hidden="true">
          {ghostText}
        </div>
      )}
      <style>{`
        .date-input-wrapper {
          /* Ensure the overlay is positioned correctly */
          position: relative;
          font-family: monospace;
        }
        .ghost-overlay {
          position: absolute;
          top: 0;
          left: 8px; /* Match input padding */
          width: calc(100% - 16px);
          height: 100%;
          display: flex;
          align-items: center;
          pointer-events: none;
          color: #ccc;
          letter-spacing: 0.3em;
          z-index: 1;
          /* Use a lighter color so that actual input text stands out */
        }
      `}</style>
    </div>
  );
}