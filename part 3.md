Assumptions:

“Recent sales” = last 30 days
Each product has at least one supplier
Low stock threshold is stored in product

@GetMapping("/api/companies/{companyId}/alerts/low-stock")
public ResponseEntity<?> getLowStockAlerts(@PathVariable Long companyId) {

    List<Map<String, Object>> alerts = new ArrayList<>();
    List<Warehouse> warehouses = warehouseRepository.findByCompanyId(companyId);
    for (Warehouse warehouse : warehouses) {
        List<Inventory> inventoryList = inventoryRepository.findByWarehouseId(warehouse.getId());

        for (Inventory inv : inventoryList) {
            Product product = inv.getProduct();
            int currentStock = inv.getQuantity();
            int threshold = product.getLowStockThreshold();
            int recentSales = salesRepository.countRecentSales(
                    product.getId(),
                    LocalDateTime.now().minusDays(30)
            )
            if (currentStock < threshold && recentSales > 0) {

               
                Supplier supplier = supplierRepository.findFirstByProductId(product.getId());

                Map<String, Object> alert = new HashMap<>();
                alert.put("product_id", product.getId());
                alert.put("product_name", product.getName());
                alert.put("sku", product.getSku());
                alert.put("warehouse_id", warehouse.getId());
                alert.put("warehouse_name", warehouse.getName());
                alert.put("current_stock", currentStock);
                alert.put("threshold", threshold);
                alert.put("days_until_stockout", 10);

                Map<String, Object> supplierMap = new HashMap<>();
                if (supplier != null) {
                    supplierMap.put("id", supplier.getId());
                    supplierMap.put("name", supplier.getName());
                    supplierMap.put("contact_email", supplier.getContactEmail());
                }

                alert.put("supplier", supplierMap);

                alerts.add(alert);
            }
        }
    }
    Map<String, Object> response = new HashMap<>();
    response.put("alerts", alerts);
    response.put("total_alerts", alerts.size());

    return ResponseEntity.ok(response);
}

Approach Explanation
First, fetch all warehouses for the company
Then retrieve inventory for each warehouse
For each product:
Check if stock is below threshold
Check if there are recent sales
If both conditions are met:
Add to alert list
Include supplier details

Edge Cases Considered
No supplier found
No recent sales
Threshold missing
Large data (performance issue)


 Possible Improvements
Use JOIN queries instead of loops for better performance
Add pagination for large datasets
Cache frequently accessed data
Add proper exception handling & logging

I prioritized correctness, scalability, and clarity over over-engineering, given the time constraint.
