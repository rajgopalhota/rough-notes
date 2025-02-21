import React, { useState, useRef, useEffect } from "react";

export default function DateField() {
  // state stores only digits (max 8 for DDMMYYYY)
  const [digits, setDigits] = useState("");
  const inputRef = useRef(null);

  // Full template string (we want to show static slashes and placeholders)
  const template = "DD/MM/YYYY";

  // Build the display string by replacing each character from template with the entered digit.
  // For positions where a digit exists, use it; otherwise, keep the character from the template.
  const displayValue = () => {
    // Remove any non-digit from the state (should already be digits only)
    const entered = digits.split("");
    let result = "";
    let digitIndex = 0;
    for (let i = 0; i < template.length; i++) {
      // Only replace positions that are digits in the template (i.e. D or M or Y)
      if (template[i] === "D" || template[i] === "M" || template[i] === "Y") {
        if (entered[digitIndex] !== undefined) {
          result += entered[digitIndex];
          digitIndex++;
        } else {
          // if no digit entered yet, leave the placeholder character from template (or blank it out if preferred)
          result += template[i];
        }
      } else {
        // Static characters (slashes)
        result += template[i];
      }
    }
    return result;
  };

  // Handle changes from user input.
  const handleChange = (e) => {
    // Remove any non-digit characters
    const cleaned = e.target.value.replace(/[^0-9]/g, "");
    // Limit to 8 digits (DDMMYYYY)
    setDigits(cleaned.slice(0, 8));
  };

  // Optionally, adjust caret position so that it doesn't get stuck in a separator.
  useEffect(() => {
    if (inputRef.current) {
      // Place caret right after the last entered digit, or at the start if none.
      // Note: since the display includes fixed characters, we need to calculate the position.
      let pos = 0;
      let digitCount = 0;
      // Walk the template until we've passed as many digit positions as in our state.
      for (let i = 0; i < template.length && digitCount < digits.length; i++) {
        if (template[i] === "D" || template[i] === "M" || template[i] === "Y") {
          digitCount++;
          pos = i + 1;
        } else {
          pos = i + 1;
        }
      }
      inputRef.current.setSelectionRange(pos, pos);
    }
  }, [digits]);

  const styles = {
    container: {
      display: "inline-block",
      position: "relative",
      fontFamily: "monospace"
    },
    input: {
      border: "1px solid #ccc",
      padding: "8px",
      borderRadius: "4px",
      width: "150px",
      fontSize: "16px",
      letterSpacing: "0.1em"
    }
  };

  return (
    <div style={styles.container}>
      <input
        ref={inputRef}
        type="text"
        value={displayValue()}
        onChange={handleChange}
        style={styles.input}
      />
    </div>
  );
}