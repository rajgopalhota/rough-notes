import React, { useState } from "react";
import { AgGridReact } from "ag-grid-react";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";

const MyTable = () => {
  const [rowData] = useState([
    { id: 1, name: "Alice", age: 25, city: "New York" },
    { id: 2, name: "Bob", age: 30, city: "London" },
    { id: 3, name: "Charlie", age: 28, city: "Paris" },
  ]);

  const [columnDefs] = useState([
    { field: "id", headerName: "ID", flex: 1 },
    { field: "name", headerName: "Name", flex: 2 },
    { field: "age", headerName: "Age", flex: 1 },
    { field: "city", headerName: "City", flex: 2 },
  ]);

  return (
    <div className="ag-theme-alpine" style={{ width: "100%", height: "100vh" }}>
      <AgGridReact
        rowData={rowData}
        columnDefs={columnDefs}
        domLayout="autoHeight"
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