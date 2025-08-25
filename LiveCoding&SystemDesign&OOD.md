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
