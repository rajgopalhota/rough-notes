import React, { useState } from "react";

export default function DateInput() {
  const [rawValue, setRawValue] = useState("");

  const handleChange = (e) => {
    // Only allow digits and limit to 8 (DDMMYYYY)
    const cleaned = e.target.value.replace(/[^0-9]/g, "").slice(0, 8);
    setRawValue(cleaned);
  };

  // Format the display string with slashes.
  // Before any digit is entered, we display nothing.
  // Once the first digit is entered, we show day as two characters, month as two, year as four,
  // filling missing characters with spaces.
  const formatDisplay = (value) => {
    if (!value) return "";
    const digits = value.split("");
    const day =
      (digits[0] ? digits[0] : " ") +
      (digits[1] ? digits[1] : " ");
    const month =
      (digits[2] ? digits[2] : " ") +
      (digits[3] ? digits[3] : " ");
    const year =
      (digits[4] ? digits[4] : " ") +
      (digits[5] ? digits[5] : " ") +
      (digits[6] ? digits[6] : " ") +
      (digits[7] ? digits[7] : " ");
    return `${day}/${month}/${year}`;
  };

  return (
    <div className="date-input-container">
      {/* Underlying input that holds raw digits */}
      <input
        className="date-input"
        type="text"
        value={rawValue}
        onChange={handleChange}
      />
      {/* Overlay is only visible when at least one digit is entered */}
      {rawValue && (
        <div className="date-overlay">{formatDisplay(rawValue)}</div>
      )}
      <style>{`
        .date-input-container {
          position: relative;
          display: inline-block;
        }
        /* The actual input: text is transparent so the overlay is visible,
           but the caret is still shown. */
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
        /* Overlay: placed exactly over the input */
        .date-overlay {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          pointer-events: none;
          border: 1px solid transparent;
          padding: 8px;
          font-family: monospace;
          letter-spacing: 0.3em;
          text-align: center;
          line-height: 1.5;
          color: #333;
          background: transparent;
          /* Ensure that selecting text only selects the actual input text */
          user-select: none;
        }
      `}</style>
    </div>
  );
}