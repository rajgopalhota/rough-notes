import React, { useState } from "react";
import { AgGridReact } from "ag-grid-react";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";

const MyTable = () => {
  const [rowData] = useState([
    { id: 1, name: "Alice Johnson", age: 25, city: "New York" },
    { id: 2, name: "Bob Williams", age: 30, city: "London" },
    { id: 3, name: "Charlie Brown", age: 28, city: "Paris" },
    { id: 4, name: "David Smith", age: 35, city: "Berlin" },
    { id: 5, name: "Emma Davis", age: 27, city: "Toronto" },
    { id: 6, name: "Frank Thomas", age: 32, city: "San Francisco" },
    { id: 7, name: "Grace White", age: 29, city: "Sydney" },
    { id: 8, name: "Hannah Lee", age: 26, city: "Singapore" },
    { id: 9, name: "Ian Clark", age: 31, city: "Dubai" },
    { id: 10, name: "Jane Wilson", age: 33, city: "Amsterdam" },
  ]);

  const [columnDefs] = useState([
    { field: "id", headerName: "ID", flex: 1 },
    { field: "name", headerName: "Name", flex: 2 },
    { field: "age", headerName: "Age", flex: 1 },
    { field: "city", headerName: "City", flex: 2 },
  ]);

  return (
    <div
      className="ag-theme-alpine custom-ag-grid"
      style={{ width: "80%", margin: "auto", height: "auto", minHeight: "400px" }}
    >
      <AgGridReact
        rowData={rowData}
        columnDefs={columnDefs}
        domLayout="autoHeight"
        rowHeight={45} // Adjusts row height for better spacing
        defaultColDef={{
          resizable: true,
          sortable: true,
          filter: true,
        }}
      />
    </div>
  );
};

export default MyTable;