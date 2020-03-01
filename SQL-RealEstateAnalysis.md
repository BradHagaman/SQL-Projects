# CodeCademy-SQL-Project-2

-- INTERMEDIATE

-- What is the percent change in average home values from 2007 to 2017 by state? Which states 
-- are seeing thehighest appreciation? avg2017 determines the average home values for each 
-- state in 2017, avg2007 does the same for 2007.


	With avg2017 AS(
		    SELECT state, substr(date,1,4), AVG(value) AS "Avg2017value"
		    FROM homevalues
		    WHERE value <> "N/a"
		    AND substr(date,1,4) like 2017
		    GROUP BY homevalues.state),
	  
    	avg2007 AS(	  
		    SELECT state, substr(date,1,4), AVG(value) AS "Avg2007value"
		    FROM homevalues
		    WHERE value <> "N/a"
		    AND substr(date,1,4) like 2007
		    GROUP BY homevalues.state)

	SELECT homevalues.state, avg2017.avg2017value AS "2017Value", avg2007.avg2007value AS     
    		"2007Value",
	  	((avg2017.avg2017value - avg2007.avg2007value)*100/avg2007.avg2007value) AS "%Change" 
	FROM homevalues
	LEFT JOIN avg2017
		ON homevalues.state = avg2017.state
	LEFT JOIN avg2007
		ON homevalues.state = avg2007.state
	GROUP BY homevalues.state
	ORDER BY "%Change" DESC;



-- How about 1997 to 2017?
-- avg2017 determines the average home values for each state in 2017, avg1997 does the same 
-- for 1997. 


	WITH avg2017 AS (
			SELECT state, substr(date,1,4), avg(value) AS "Avg2017value"
			FROM homevalues
			WHERE value <> "N/a"
			AND substr(date,1,4) LIKE 2017
			GROUP BY homevalues.state),

	  avg1997 AS (
			SELECT state, substr(date,1,4), avg(value) AS "Avg1997value"
			FROM homevalues
			WHERE value <> "N/a"
			AND substr(date,1,4) LIKE 1997
			GROUP BY homevalues.state)

	SELECT homevalues.state, avg2017.avg2017value, avg1997.avg1997value, ((avg2017.avg2017value-avg1997.avg1997value)*100/avg1997.avg1997value) AS "%Change"
	FROM homevalues
	LEFT JOIN avg2017
		ON homevalues.state = avg2017.state
	LEFT JOIN avg1997
		ON homevalues.state = avg1997.state
	GROUP BY homevalues.state
	ORDER BY "%Change" DESC;






--BASIC


-- How many distrinct zip codes are in this dataset?

	SELECT COUNT(zipcode) AS "# of ZipCodes"
	FROM homevalues;


-- How many zip codes are from each state? What state has the most?

	SELECT *, COUNT(zipcode) AS "# of ZipCodes"
	FROM homevalues
	GROUP BY state
	ORDER BY COUNT(zipcode) DESC;

-- What range of years are represented in the data?

	SELECT MIN(substr(date, 1, 4)), max(substr(date, 1, 4))
	FROM homevalues;

-- Using the most recent month of data available,  
-- what is the range of estimated home values across the nation

	SELECT max(date), min(value), max(value)
	FROM homevalues
	WHERE value <> "N/a";

-- Using the most recent month of data available,
-- Which states have the highest average home values?

	SELECT state, max(date),  avg(value)
	FROM homevalues
	GROUP BY state
	ORDER BY avg(value) DESC
	LIMIT 10;

-- Which states have the lowest?

	SELECT state, max(date),  avg(value)
	FROM homevalues
	GROUP BY state
	ORDER BY avg(value) ASC
	LIMIT 10;

-- What about in the year 2009?

	SELECT state, substr(date,1,4),  avg(value)
	FROM homevalues
	WHERE substr(date,1,4) LIKE 2009
	GROUP BY state
	ORDER BY avg(value) DESC;

