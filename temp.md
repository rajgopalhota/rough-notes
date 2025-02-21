import React, { useState } from "react";

export default function DateInput() {
  const [rawValue, setRawValue] = useState("");

  // Format the value to a full display (only after the user starts typing)
  const formatDisplay = (value) => {
    if (!value) return "";
    const digits = value.split("");
    const day = (digits[0] || " ") + (digits[1] || " ");
    const month = (digits[2] || " ") + (digits[3] || " ");
    const year = (digits[4] || " ") + (digits[5] || " ") + (digits[6] || " ") + (digits[7] || " ");
    return `${day}/${month}/${year}`;
  };

  const handleChange = (e) => {
    // Only allow digits and limit to 8 characters (DDMMYYYY)
    const cleaned = e.target.value.replace(/[^0-9]/g, "").slice(0, 8);
    setRawValue(cleaned);
  };

  // Use a CSS variable to pass the formatted string to the pseudo-element
  const wrapperStyle = {
    "--formatted-date": `"${rawValue ? formatDisplay(rawValue) : ""}"`,
    position: "relative",
    display: "inline-block",
  };

  return (
    <div style={wrapperStyle} className="date-input-wrapper">
      <input
        type="text"
        value={rawValue}
        onChange={handleChange}
        placeholder="DDMMYYYY"
        style={{
          border: "1px solid #ccc",
          padding: "8px",
          borderRadius: "4px",
          width: "150px",
          fontFamily: "monospace",
          letterSpacing: "0.3em",
        }}
      />
      <style>{`
        .date-input-wrapper::after {
          content: var(--formatted-date);
          position: absolute;
          left: 8px; /* same as input padding */
          top: 50%;
          transform: translateY(-50%);
          pointer-events: none;
          font-family: monospace;
          letter-spacing: 0.3em;
          color: #ccc;
        }
      `}</style>
    </div>
  );
}