package org.example;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Arrays;


public class WarehouseMenager implements Warehouse {
    private Map<String, Map<SupportedMetalType, Double>> clientMetalInventory = new HashMap<>();
    private static final double MAX_WAREHOUSE_CAPACITY = 1000.0; // Example maximum warehouse capacity


    public void addMetalIngot(String clientId, SupportedMetalType metalType, double mass)
            throws ClientNotFoundException, ProhibitedMetalTypeException, FullWarehouseException {
        if (!clientMetalInventory.containsKey(clientId)) {
            throw new ClientNotFoundException();
        }


        if (metalType.isProhibited()) {
            throw new ProhibitedMetalTypeException();
        }

        Map<SupportedMetalType, Double> clientInventory = clientMetalInventory.get(clientId);


        double totalVolumeOccupied = clientInventory.values().stream().mapToDouble(Double::doubleValue).sum();


        if (totalVolumeOccupied + (mass / metalType.getDensity()) > MAX_WAREHOUSE_CAPACITY) {
            throw new FullWarehouseException();
        }


        clientInventory.put(metalType, clientInventory.getOrDefault(metalType, 0.0) + mass);
    }
    public Map<SupportedMetalType, Double> getMetalTypesToMassStoredByClient(String clientId) {
        return clientMetalInventory.getOrDefault(clientId, new HashMap<>());
    }

    public double getTotalVolumeOccupiedByClient(String clientId) {
        return clientMetalInventory.getOrDefault(clientId, new HashMap<>())
                .values().stream().mapToDouble(Double::doubleValue).sum();
    }
    public List<SupportedMetalType> getStoredMetalTypesByClient(String clientId) {
        return List.copyOf(clientMetalInventory.getOrDefault(clientId, new HashMap<>()).keySet());
    }
}
