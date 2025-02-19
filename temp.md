import React, { useState, useEffect } from "react";
import { AgGridReact } from "ag-grid-react";
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import { Link } from "react-router-dom";

const Top10Fulfillments = () => {
    const [rowData, setRowData] = useState([]);

    useEffect(() => {
        const fetchFulfillments = async () => {
            try {
                const response = await fetch("/api/top-fulfillments?date=yesterday"); // Replace with actual API
                if (!response.ok) throw new Error("Failed to fetch data");
                const data = await response.json();
                setRowData(data);
            } catch (error) {
                console.error("Error fetching data:", error);
            }
        };

        fetchFulfillments();
    }, []);

    const columnDefs = [
        { headerName: "Customer ID", field: "customerId", sortable: true, filter: true, resizable: true },
        { headerName: "Customer Name", field: "customerName", sortable: true, filter: true, resizable: true },
        { headerName: "Reward Item Name", field: "rewardCatalogItemName", sortable: true, filter: true, resizable: true },
        { headerName: "Quantity", field: "fulfillmentQuantity", sortable: true, filter: true, resizable: true }
    ];

    return (
        <div className="p-6">
            <nav aria-label="breadcrumb" role="navigation" className="mb-4">
                <ol className="flex space-x-2 text-blue-600">
                    <li className="breadcrumb-item">
                        <Link to="/">Home</Link>
                    </li>
                    <li className="breadcrumb-item">
                        <Link to="/fulfillments">Fulfillments</Link>
                    </li>
                </ol>
            </nav>

            <h2 className="text-2xl font-bold mb-4">Top 10 Fulfillments - Yesterday</h2>

            <div className="ag-theme-alpine" style={{ height: 500, width: "100%" }}>
                <AgGridReact
                    rowData={rowData}
                    columnDefs={columnDefs}
                    pagination={true}
                    paginationPageSize={10}
                    domLayout="autoHeight"
                />
            </div>
        </div>
    );
};

export default Top10Fulfillments;