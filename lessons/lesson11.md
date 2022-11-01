Lesson 11
========

**Lesson Plan**  

total time 150 minutes. 

1.   Housekeeping (2:00 pm - 5 min)  
   -   Chris note about last minute projects
   -   Nov 14 deadline for meetings with us

2.   Abigail Flemming (2:05 pm - 60 min)

3.   Break (3:05 pm - 5 min)

5.   SQL (4:00 pm - 15 min)

3.   Student Presentations (3:40 pm - 20 min)

4.   Vector Relationships, Structures, and Tools (3:10 pm - 30 min)
   -   Overlays
      - pay attention to attributes!!
   -   Clipping

6.   SQL demonstration (4:15 pm - 30 min)
   -   intersection of features
      -   https://blog.cleverelephant.ca/2019/07/postgis-overlays.html
   -   estimated population impact of 100 year flood (see below)
   -   join on zipcodes (column) and within census tracts (geom)


SELECT fzone, ST_Transform(geom,4269) as geom 
INTO fz100 
FROM public.mdc_flood_hazard 
WHERE fzone LIKE 'AH';

SELECT total_population,geom 
INTO dade_tracts 
FROM acs_2019_5yr_tract_12_florida_dvmt 
WHERE countyfp = '086'

SELECT dade_tracts.total_population, dade_tracts.geom 
FROM dade_tracts, fz100
WHERE ST_Intersects(dade_tracts.geom, fz100.geom)

SELECT sum(dade_tracts.total_population) 
FROM dade_tracts, fz100
WHERE ST_Intersects(dade_tracts.geom, fz100.geom)

SELECT 
   dade_tracts.total_population, 
   ROUND(dade_tracts.total_population * (ST_Area(ST_Intersection(dade_tracts.geom,fz100.geom))/ST_Area(dade_tracts.geom))) as prop_est, 
   dade_tracts.geom 
FROM dade_tracts, fz100
WHERE ST_Intersects(dade_tracts.geom, fz100.geom)

SELECT ROUND(SUM( 
   dade_tracts.total_population * (ST_Area(ST_Intersection(dade_tracts.geom,fz100.geom))/ST_Area(dade_tracts.geom)) 
))
FROM dade_tracts, fz100
WHERE ST_Intersects(dade_tracts.geom, fz100.geom)

SELECT ROUND(SUM( 
   dade_tracts.total_population * (ST_Area(ST_Intersection(dade_tracts.geom,fz100.geom))/ST_Area(dade_tracts.geom)) 
))
INTO tract_prop_est
FROM dade_tracts, fz100
WHERE ST_Intersects(dade_tracts.geom, fz100.geom)

DROP TABLE fz100;
DROP TABLE dade_tracts;
DROP TABLE tract_prop_est;


