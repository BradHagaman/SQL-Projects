# CodeCademy-DSIP2

--BASIC


-- Top 100 tracks by appearance in playlists.
	
	
	SELECT tracks.name, COUNT(*) AS "How many playlists"
	FROM playlist_track
	LEFT JOIN tracks
		ON playlist_track.trackid = tracks.TrackId
	GROUP BY playlist_track.TrackId
	ORDER BY COUNT(playlist_track.trackid) DESC
	LIMIT 100;


-- Top 10 tracks by revenue
	
	
	SELECT tracks.name, invoice_items.trackid, 
		SUM(invoice_items.unitprice)
	FROM invoice_items
	LEFT JOIN tracks
		ON invoice_items.trackid = tracks.TrackId
	GROUP BY invoice_items.TrackId
	ORDER BY SUM(invoice_items.unitprice) DESC
	LIMIT 10;


-- Top 10 albums by revenue
	
	
	SELECT albums.title, tracks.albumid, 
		SUM(invoice_items.UnitPrice)
	FROM tracks
	INNER JOIN albums
		ON tracks.albumid = albums.AlbumId
	INNER JOIN invoice_items
		ON tracks.trackid = invoice_items.trackid
	GROUP BY tracks.AlbumId
	ORDER BY SUM(invoice_items.unitprice) DESC
	LIMIT 10;


-- Top 5 genres by revenue
	
	
	SELECT  tracks.genreid, genres.name, SUM(invoice_items.unitprice)
	FROM tracks
	INNER JOIN invoice_items
		ON tracks.trackid = invoice_items.TrackId
	INNER JOIN genres
		ON tracks.genreid = genres.GenreId
	GROUP BY tracks.GenreId
	ORDER BY SUM(invoice_items.unitprice) DESC
	LIMIT 5;


-- Top 10 countries by revenue
	
	
	SELECT invoices.BillingCountry, SUM(invoice_items.unitprice)  AS "Revenue"
	FROM invoices
	INNER JOIN customers
		ON invoices.BillingCountry = customers.Country
	INNER JOIN invoice_items
		ON invoices.invoiceid = invoice_items.InvoiceId
	GROUP BY invoices.BillingCountry
	ORDER BY SUM(invoice_items.unitprice) DESC
	LIMIT 10;


-- Percentage of sales by country
	
	
	SELECT invoices.BillingCountry, SUM(invoice_items.unitprice) * 100 / (SELECT SUM(invoice_items.unitprice) FROM invoice_items) AS "Country Revenue %" 
	FROM invoice_items
	JOIN invoices
		ON invoice_items.InvoiceId = invoices.InvoiceId
	GROUP BY invoices.BillingCountry
	ORDER BY SUM(invoice_items.unitprice) DESC
	LIMIT 10;


-- Number of customers supported by rep
	
	
	SELECT employees.Title, employees.EmployeeId, employees.FirstName, employees.LastName, COUNT(customers.customerid) AS "# of Clients"
	FROM employees
	INNER JOIN customers
		ON employees.EmployeeId = customers.SupportRepId
	GROUP BY employees.EmployeeId
	ORDER BY COUNT(customers.customerid) DESC;


-- Average revenue by sale
	
	
	SELECT AVG(unitprice) AS "Average price"
	FROM invoice_items;


-- Total sales
	
	
	SELECT SUM(unitprice) AS "Total Sales"
	FROM invoice_items;


-- INTERMEDIATE
-- Do longer or shorter albums generate more revenue? Nested queries: track_value determines 
-- the total value of the track, album_length determines length of each album, and full_query
-- brings these together into one query. The final query then orders the results on the average 
-- album value.


	WITH full_query AS (
		WITH track_value AS (
			SELECT trackid, SUM(UnitPrice) AS "SumPrice"
			FROM invoice_items
			GROUP BY invoice_items.TrackId),
		album_length AS (
			SELECT AlbumId, COUNT(*)
			FROM tracks
			GROUP BY AlbumId)
	SELECT tracks.AlbumId, SUM(track_value.SumPrice) AS "AlbumValue", SUM(tracks.milliseconds) AS "AlbumLength"
	FROM tracks
	LEFT JOIN track_value
		ON tracks.AlbumId = track_value.TrackId
	LEFT JOIN albums
		ON tracks.AlbumId = albums.AlbumId
	GROUP BY tracks.AlbumId)

	SELECT AlbumId,AVG(AlbumValue), AlbumLength
	FROM full_query
	GROUP BY AlbumLength
	ORDER BY AVG(AlbumValue) DESC;


-- Is the number of times a track appears in any playlist a good indicator of revenue?
-- playlist_numbers query establishes number of playlists a track appears in, and then this field
-- is grouped and the unit price of the invoiced items averaged.


	WITH playlist_numbers AS
		(SELECT trackid, COUNT(*) AS TrackCount
		FROM playlist_track
		GROUP BY TrackId)	
	SELECT playlist_numbers.TrackCount AS "PlaylistAppearances", AVG(invoice_items.unitprice) AS "Sales"
	FROM invoice_items
	INNER JOIN playlist_numbers
		ON invoice_items.TrackId = playlist_numbers.TrackId
	GROUP BY playlist_numbers.TrackCount
	ORDER BY AVG(invoice_items.unitprice) DESC;
