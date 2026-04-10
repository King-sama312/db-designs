Here is diagram:

<img width="2231" height="1186" alt="Smart-elevator-control-db-design" src="https://github.com/user-attachments/assets/13b49ca1-270a-4081-b446-089d35da6c8e" />


Here is dbml for dbdiagram.io

```
// === RELATION DEFINITIONS ===
// Buildings 1──< Floors
// Buildings 1──< Elevator_shafts
// Elevator_shafts 1──1 Elevators
// Elevators 1──< Elevator_floor_access
// Floors 1──< Elevator_floor_access
// Buildings 1──< Ride_requests
// Floors 1──< Ride_requests (as source/destination)
// Ride_requests 1──1 Ride_assignments
// Elevators 1──< Ride_assignments
// Elevators 1──1 Elevator_status
// Elevators 1──< Maintenance_records
// Floors 1──< Elevator_status (current_floor_id, optional)

Table Buildings {
  id integer [primary key]
  name varchar
  address varchar
  city varchar 
  created_at timestamp
}

Table Floors {
  id integer [primary key]
  building_id integer [ref: > Buildings.id]  // Fixed typo from "buidling_id"
  floor_number integer
  floor_label varchar // eg - lobby, parking
}

Table Elevators {
  id integer [primary key]
  shaft_id integer [ref: > Elevator_shafts.id, unique]
  model varchar
  capacity_kg integer
  max_speed_mps integer
  installation_date timestamp
}

Table Elevator_shafts {
  id integer [primary key]
  building_id integer [ref: > Buildings.id]
  shaft_label varchar
}

Table Elevator_floor_access {
  id integer [primary key]
  elevator_id integer [ref: > Elevators.id]
  floor_id integer [ref: > Floors.id]
  // Composite unique constraint recommended:
  // [unique: elevator_id, floor_id]
}

Table Ride_requests {
  id integer [primary key]
  building_id integer [ref: > Buildings.id]
  source_floor_id integer [ref: > Floors.id]
  destination_floor_id integer [ref: > Floors.id]
  requested_at timestamp
  status enum('pending', 'assigned', 'cancelled')
}

Table Ride_assignments {
  id integer [primary key]
  request_id integer [ref: > Ride_requests.id, unique] // Enforces 1:1 with request
  elevator_id integer [ref: > Elevators.id]
  assigned_at timestamp
  dispatched_at timestamp
  completed_at timestamp
  status enum('allocated', 'in_progress', 'completed', 'failed')
}

Table Elevator_status {
  id integer [primary key]
  elevator_id integer [ref: > Elevators.id, unique] // 1:1 with Elevator
  current_status enum('idle', 'moving_up', 'moving_down', 'doors_open', 'maintenance', 'offline')
  current_floor_id integer [ref: > Floors.id, null] // Optional: elevator may be between floors
  last_updated timestamp
  is_operational boolean
}

Table Maintenance_records {
  id integer [primary key]
  elevator_id integer [ref: > Elevators.id]
  start_time timestamp
  end_time timestamp [null]
  type enum('scheduled', 'emergency', 'inspection')
  description varchar
  technician_name varchar
  cost_in_rs integer
  status enum('scheduled', 'in_progress', 'completed')
}
```
