Here is the image:
<img width="2979" height="551" alt="Comic-Con-parking-system-db" src="https://github.com/user-attachments/assets/a92c1b2f-7a8d-42a4-bb49-50afd3624551" />



Here is dbml for diagram.io:
```
Table Vehicle_Category {
  id serial [primary key]
  name varchar(50) [not null, note: 'bike, car, suv, cab, ev']
}

Table Vehicle {
  id serial [primary key]
  license_plate varchar(20) [not null, unique]
  owner_name varchar(100) [not null]
  driver_name varchar(100) [note: 'Populated only for cab entries']
  vehicle_category_id integer [not null, ref: > Vehicle_Category.id]
  created_at timestamp
}

Table Access_Category {
  id serial [primary key]
  name varchar(100) [not null, note: 'cosplayers_with_props, exhibitors, creators, VIP_guests, staff_members, EV_charging']
}

Table Parking_Zone {
  id serial [primary key]
  name varchar(100) [not null]
  access_category_id integer [ref: > Access_Category.id, note: 'null = open to all']
  total_spots integer
}

Table Parking_Level {
  id serial [primary key]
  zone_id integer [not null, ref: > Parking_Zone.id]
  level_name varchar(10) [not null, note: 'e.g. L1, L2, B1']
  allowed_vehicle_category_id integer [ref: > Vehicle_Category.id, note: 'null = all types allowed']
}

Table Parking_Spot {
  id serial [primary key]
  level_id integer [not null, ref: > Parking_Level.id]
  spot_number varchar(10) [not null]
  is_reserved boolean [default: false]
  status varchar(20) [not null, note: 'available, occupied, maintenance']
}

Table Parking_Ticket {
  id serial [primary key]
  ticket_number varchar(50) [not null, unique]
  vehicle_id integer [not null, ref: > Vehicle.id]
  issued_at timestamp [not null]
}

Table Parking_Session {
  id serial [primary key]
  vehicle_id integer [not null, ref: > Vehicle.id]
  spot_id integer [not null, ref: > Parking_Spot.id]
  ticket_id integer [not null, ref: - Parking_Ticket.id]
  entry_time timestamp [not null]
  exit_time timestamp [note: 'null while vehicle is still parked']
  fee_amount decimal(10,2) [note: 'calculated on exit']
}

Table Payment {
  id serial [primary key]
  session_id integer [not null, ref: - Parking_Session.id]
  transaction_id varchar(200)
  amount decimal(10,2) [not null]
  method varchar(20) [note: 'upi, bank_transfer, cash']
  status varchar(20) [not null, note: 'pending, paid, failed, refunded']
  paid_at timestamp
  created_at timestamp
  updated_at timestamp
}
```
