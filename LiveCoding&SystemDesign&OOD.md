# Live Coding：

1. 确认需求，了解边界
2. 边界防护，错误判断
3. 思路说明，最好有多个思路
4. 写代码 并 走查
5. 测试代码
6. 注释

# System Design

## General framework

Part1: understand problem and establish design scope (5 min)
At the end of this part, Should shortlist the feature(functional req), along with a few non-functional req

Part2: high level design

1. Define API(Restful) with params and responses for fulfilling functional req / or if two way communication is needed then websocket
2. high level design diagram (lb/services/db), do not mention any specific type of db(mysql/oracle) in this stage
3. data model and schema, can talk about which db to use and indexing and sharding

Part3: deep dive

# OOD

## Parking lot

Absolutely! Here’s a solid Object-Oriented Design (OOD) interview question that tests your ability to model real-world systems using principles like encapsulation, inheritance, and polymorphism:

---

🧩 OOD Interview Question: Design a Parking Lot System

Prompt:
Design a parking lot system that supports multiple levels, each with multiple rows of parking spots. There are different types of vehicles (motorcycles, cars, buses), and each vehicle type can only park in certain types of spots. The system should be able to:

• Park a vehicle
• Remove a vehicle
• Track available spots
• Handle edge cases like a bus needing 5 consecutive spots

Requirements:

• Motorcycles can park in any spot
• Cars can park in regular or large spots
• Buses need 5 large spots in a row

```java

import java.util.ArrayList;
import java.util.List;

class Vehicle {
    public Spot spot = null;
    public void park(Spot spot) {
        this.spot = spot;
        spot.parkVehicle(this);
    }
    public void leave() {
        spot.removeVehicle();
        spot = null;
    }
}

class Car extends Vehicle {

}

class Bus extends Vehicle {
    public List<Spot> spots;

    public void park(List<Spot> spots) {
        this.spots = spots;
        for (Spot spot : spots) {
            spot.parkVehicle(this);
        }
    }

    @Override
    public void leave() {
        for (Spot spot : spots) {
            spot.removeVehicle();
        }
        spots = null;
    }


}

class MotorCycle extends Vehicle {

}

class Spot {
    public Vehicle vehicleParked = null;

    public void parkVehicle(Vehicle vehicle) {
        vehicleParked = vehicle;
        isParked = true;
    }

    public void removeVehicle() {
        vehicleParked = null;
        isParked = false;
    }

    public boolean getIsParked() {
        return isParked;
    }

    public boolean isParked = false;

}

class RegularSpot extends Spot {

}

class LargeSpot extends Spot {

}

class SmallSpot extends Spot {

}

class Level {

    List<Spot> regularSpots;
    List<Spot> largeSpots;
    List<Spot> smallSpots;

    //use segment tree to maintain the longest consecutive empty spot
    // [1,10] longest is 6, the index is 3, [3,8] is empty, available for bus.

    List<List<Spot>> carAvailableSpot;
    List<List<Spot>> motorAvailableSpot;
    List<List<Spot>> busAvailableSpot;

    public Level(int rowWidth) {
        regularSpots = new ArrayList<Spot>(rowWidth);
        for (int i = 0;i<rowWidth;i++){
            regularSpots.add(new RegularSpot());
        }
        largeSpots = new ArrayList<>(rowWidth);
        for (int i = 0;i<rowWidth;i++){
            largeSpots.add(new LargeSpot());
        }
        smallSpots = new ArrayList<>(rowWidth);
        for (int i = 0;i<rowWidth;i++){
            smallSpots.add(new SmallSpot());
        }
        carAvailableSpot = new ArrayList<>();
        carAvailableSpot.add(regularSpots);
        carAvailableSpot.add(largeSpots);
        motorAvailableSpot = new ArrayList<>();
        motorAvailableSpot.add(regularSpots);
        motorAvailableSpot.add(largeSpots);
        motorAvailableSpot.add(smallSpots);
        busAvailableSpot = new ArrayList<>();
        busAvailableSpot.add(largeSpots);

    }

    public boolean isConsecutiveEmpty(List<Spot> spots, int begin) {
        if (begin+5>=spots.size()) {
            return false;
        }
        for (int i = begin;i<begin+5;i++){
            if (spots.get(i).getIsParked()) {
                return false;
            }
        }
        return true;
    }

    public boolean park(Vehicle vehicle) {
        if (vehicle instanceof Car) {
           for (List<Spot> spotRow : carAvailableSpot) {
               for (Spot spot : spotRow) {
                   if (!spot.getIsParked()) {
                       vehicle.park(spot);
                       return true;
                   }
               }
           }
           return false;

        } else if (vehicle instanceof MotorCycle) {
            for (List<Spot> spotRow : motorAvailableSpot) {
                for (Spot spot : spotRow) {
                    if (!spot.getIsParked()) {
                        vehicle.park(spot);
                        return true;
                    }
                }
            }
            return false;

        } else if (vehicle instanceof Bus) {
            Bus bus = (Bus)vehicle;
            for (List<Spot> spotRow : busAvailableSpot) {
                for (int i = 0;i<spotRow.size();i++) {
                    if (isConsecutiveEmpty(spotRow, i)) {
                        bus.park(spotRow.subList(i,i+5));
                        return true;
                    }
                }
            }
            return false;
        } else {
            throw new RuntimeException("Invalid Vehicle type");
        }
    }
}
public class ParkingLot {
    List<Level> levels;

    public ParkingLot(int levelCount) {
        levels = new ArrayList<>(levelCount);
    }

    public boolean park(Vehicle vehicle) {
        for (Level level: levels) {
            if (level.park(vehicle)) {
                return true;
            }
        }
        return false;
    }

}
```

## CSV query

```java

class ExecutionEngine {
    Map<String, QueryResult> map;

    public ExecutionEngine() {
        map = new HashMap<>();
        readCountry();
        readLanguage();

    }

    private void readCountry() {
        String content = "country,latitude,longitude,country_name\n" +
                "AD,42.546245,1.601554,Andorra\n" +
                "AE,23.424076,53.847818,United Arab Emirates\n" +
                "AF,33.93911,67.709953,Afghanistan";
        read("countries.csv", content);
    }

    private void readLanguage() {
        String content = "country_name,country_code_name,country_code,lang_name,lang_code\n" +
                "Afghanistan,af,93,Pashto,ps\n" +
                "Afghanistan,af,93,Dari,dr\n" +
                "Albania,al,355,Albanian,sq";
        read("language.csv", content);
    }

    private void read(String fileName, String content) {
        String[] lines = content.split("\n");
        List<List<String>> contentList = Arrays.stream(lines).map(this::parseLine).collect(Collectors.toList());
        QueryResult queryResult = new QueryResult(contentList.get(0), contentList.subList(1, contentList.size()));
        map.put(fileName, queryResult);
    }

    private List<String> parseLine(String line) {
        String[] values = line.split(",");
        return Arrays.stream(values).collect(Collectors.toList());
    }

    public QueryResult from(String fileName) {
        return map.getOrDefault(fileName, null);
    }
}

class QueryResult{
    List<String> header;
    List<List<String>> rows;
    Map<String, Integer> headerIdx;

    public QueryResult(List<String> header, List<List<String>> rows) {
        this.header = header;
        this.rows = rows;
        this.headerIdx = getHeaderIdx(header);
    }

    public void print() {
        printLine(header);
        rows.forEach(this::printLine);
    }

    private void printLine(List<String> row){
        System.out.println(String.join(",", row));
    }

    private Map<String, Integer> getHeaderIdx(List<String> header) {
        Map<String, Integer> map = new HashMap<>();
        for (int i = 0;i<header.size();i++){
            map.put(header.get(i), i);
        }
        return map;
    }

    public QueryResult select(List<String> columns){
        List<List<String>> ans = new ArrayList<>();
        rows.forEach((row) -> {
            List<String> selectedRow = new ArrayList<>();
            for (int i = 0;i<columns.size();i++){
                int id = headerIdx.get(columns.get(i));
                selectedRow.add(row.get(id));
            }
            ans.add(selectedRow);
        });
        return new QueryResult(columns,ans);
    }

    public QueryResult sort(String column){
        int id = headerIdx.getOrDefault(column, -1);
        if (id == -1){
            return new QueryResult(this.header, this.rows);
        }
        List<List<String>> ans = new ArrayList<>(rows);
        Collections.sort(ans, Comparator.comparingDouble(r -> Double.parseDouble(r.get(id))));
        return new QueryResult(this.header, ans);
    }

    public QueryResult take(int n) {
        return new QueryResult(this.header, this.rows.subList(0,Math.min(n,this.rows.size())));
    }

    public QueryResult count(String column) {
        int id = headerIdx.getOrDefault(column, -1);
        if (id == -1) {
            return new QueryResult(Arrays.asList(column, "count"), new ArrayList<>());
        }
        Map<String, Integer> ts = new TreeMap<>();
        for (List<String> row:rows){
            String value = row.get(id);
            ts.put(value, ts.getOrDefault(value, 0)+1);
        }
        List<List<String>> ans = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : ts.entrySet()) {
            ans.add(Arrays.asList(entry.getKey(), String.valueOf(entry.getValue())));
        }
        Collections.sort(ans, (r1, r2) -> {
            return Integer.valueOf(r2.get(1)) - Integer.valueOf(r1.get(1));
        });
        return new QueryResult(Arrays.asList(column, "count"), ans);
    }



}
```
