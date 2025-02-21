import React, { useState, useRef, useEffect } from "react";

export default function DateField({ defaultValue = "" }) {
  const inputRef = useRef(null);
  // We'll store the raw digits (only numbers) in state.
  const [digits, setDigits] = useState(() => {
    // If a default value is provided in the form DD/MM/YYYY, remove the slashes.
    return defaultValue.replace(/[^0-9]/g, "");
  });

  // Format the digits to a DD / MM / YYYY string.
  const formatDigits = (raw) => {
    let day = raw.slice(0, 2);
    let month = raw.slice(2, 4);
    let year = raw.slice(4, 8);
    let result = day;
    if (day.length === 2) {
      // Append slash with spacing when day is complete.
      result += " / ";
    }
    result += month;
    if (month.length === 2) {
      result += " / ";
    }
    result += year;
    return result;
  };

  const handleChange = (e) => {
    // Remove any non-digit characters and limit to 8 digits.
    const cleaned = e.target.value.replace(/[^0-9]/g, "").slice(0, 8);
    setDigits(cleaned);
  };

  // When the digits change, we want to update the caret position.
  // We compute the caret position based on the number of digits and inserted slashes.
  useEffect(() => {
    if (inputRef.current) {
      let pos = digits.length;
      // If the day is complete, we have inserted " / " (3 extra characters) after the day.
      if (digits.length > 1) {
        pos += 3; // after day if day is complete
      }
      // If the month is complete, add additional spacing for month slash.
      if (digits.length > 3) {
        pos += 3; // after month if month is complete
      }
      // Set caret position
      inputRef.current.setSelectionRange(pos, pos);
    }
  }, [digits]);

  return (
    <div style={styles.container}>
      <input
        ref={inputRef}
        type="text"
        value={formatDigits(digits)}
        onChange={handleChange}
        placeholder="DD / MM / YYYY"
        style={styles.input}
      />
    </div>
  );
}

const styles = {
  container: {
    display: "inline-block",
    position: "relative",
  },
  input: {
    border: "1px solid #ccc",
    padding: "8px",
    borderRadius: "4px",
    width: "200px",
    fontFamily: "monospace",
    fontSize: "16px",
    // You can adjust letterSpacing if you need more gap around the slashes:
    letterSpacing: "0.1em",
  },
};