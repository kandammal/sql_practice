/*Ram and his 3 friends want to sit together in a movie theater, and they insist on sitting in the same row. 
The theater has three rows[A,B,C], each with ten chairs[A1-A10,B1-B10,C1-C10].They prefer to occupy consecutive chairs 
within the same row and are not willing to sit in chairs from different rows. 
I'm here to assist them in finding a row where four adjacent chairs are unoccupied.*/

create schema cinema;

create table cinema_tickets(seat_number varchar(100) not null, occupancy int);

insert into cinema_tickets(seat_number, occupancy) values('A1',1),('A2',1),('A3',0),('A4',0),('A5',0),('A6',0),
('A7',1),('A8',1),('A9',0),('A10',0),('B1',0),('B2',0),('B3',0),('B4',1),('B5',1),('B6',1),
('B7',1),('B8',0),('B9',0),('B10',0),('C1',0),('C2',1),('C3',0),('C4',1),('C5',1),('C6',0),
('C7',1),('C8',0),('C9',0),('C10',1);

select * from cinema_tickets;

with cte as(
select *,
lead(occupancy,1) over (partition by substr(seat_number,1,1)) as s1,
lead(occupancy,2) over (partition by substr(seat_number,1,1)) as s2,
lead(occupancy,3) over (partition by substr(seat_number,1,1)) as s3,
occupancy+
lead(occupancy,1) over (partition by substr(seat_number,1,1))+
lead(occupancy,2) over (partition by substr(seat_number,1,1))+
lead(occupancy,3) over (partition by substr(seat_number,1,1)) as sum_of_occupancy,
lead(seat_number,3) over(partition by substr(seat_number,1,1)) as end_seat
from cinema_tickets)
select seat_number,end_seat from cte where sum_of_occupancy=0;
