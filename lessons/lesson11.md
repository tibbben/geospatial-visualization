Lesson 11
========

**Lesson Plan**  

total time 150 minutes. 

1.   Housekeeping (2:00 pm - 10 min)  
   -   Chris note about last minute projects
   -   Nov 14 deadline for meetings with us
   -   GIS Day

2.   Abigail Flemming (2:10pm - 60 min)

3.   Break (3:10 - 5 min)

4.   Review of Joins (3:15 - 25 min)
   -   quickly review week 8 slides
   -   questions from homework

5.   Student presentations (3:40 - 20 min)

6.   Break (4:00 pm - 5 min)

7.   SQL (4:05 pm - 15 min)

8.   SQL demonstration (4:15 pm - 30 min)
   -   intersection of features
      -   https://blog.cleverelephant.ca/2019/07/postgis-overlays.html
   -   estimated population impact of 100 year flood (see below)
   -   join on zipcodes (column) and within census tracts (geom)


SELECT fzone, ST_Transform(geom,4269) as geom 
INTO fz100 
FROM public.mdc_flood_hazard 
WHERE fzone LIKE 'AH';

SELECT geoid, total_population, geom 
INTO dade_tracts 
FROM mdc_2021_acs_5yr_tract_dvmt 
WHERE countyfp = '086'

SELECT DISTINCT dade_tracts.geoid, dade_tracts.total_population, dade_tracts.geom
INTO tracts_fz100_intersect
FROM dade_tracts, fz100
WHERE ST_Intersects(dade_tracts.geom, fz100.geom)

SELECT sum(total_population) 
FROM tracts_fz100_intersect

SELECT
   dade_tracts.geoid,
   dade_tracts.total_population, 
   ROUND(dade_tracts.total_population * (
         ST_Area(
            ST_Intersection(dade_tracts.geom,fz100.geom)
         )/ST_Area(dade_tracts.geom)
      )
   ) as prop_est, 
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
DROP TABLE tracts_fz100_intersect;


