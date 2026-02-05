# Formula 1 SQL Database Project (2023–2025)

## Project Purpose
This project demonstrates SQL database design and querying skills using real-world Formula 1 data.  
It models Formula 1 seasons, races, drivers, teams, and race results across the 2023–2025 seasons.

The focus is on:
- Relational database design
- Foreign key relationships
- Data integrity
- Querying race and season-level statistics
- Converting race time formats into usable numerical values

---

## Database Overview

The database is structured to avoid data duplication and follow normalization best practices.

### Tables Included
- seasons
- races
- drivers
- teams
- results

Each table represents a real-world entity in Formula 1 and is connected using foreign keys.

---

## Design Choices

- A single `drivers` table is used instead of one per season to avoid duplication.
- Seasons are stored separately and referenced by the `races` table.
- Each race is uniquely identified with `race_id`.
- Race results are stored in a junction-style table (`results`) linking:
  - races
  - drivers
  - teams
- Points are stored per race, allowing season totals to be calculated dynamically.
- Race time is stored in seconds for easier calculations and comparisons.

---

## Time Conversion Logic

Formula 1 race times are often given in formats such as:

- `1:35:39.435`
- `+57.806s`

These were converted into seconds using the following logic:

- Minutes × 60
- Hours × 3600
- Add remaining seconds and milliseconds

Example:
- `1:35:39.435`  
  = (1 × 3600) + (35 × 60) + 39.435  
  = 5739.435 seconds

Gap times (e.g. `+57.806s`) were added to the winner’s total time to calculate finishing times.

---

## Example Queries

### Total points per driver (all seasons)
```sql
SELECT d.first_name, d.last_name, SUM(r.points) AS total_points
FROM results r
JOIN drivers d ON r.driver_id = d.driver_id
GROUP BY d.driver_id
ORDER BY total_points DESC;
```

### Points per driver per season
```sql
SELECT ra.season, d.first_name, d.last_name, SUM(r.points) AS season_points
FROM results r
JOIN races ra ON r.race_id = ra.race_id
JOIN drivers d ON r.driver_id = d.driver_id
GROUP BY ra.season, d.driver_id
ORDER BY ra.season, season_points DESC;
```

---

## Average Finishing Position Per Driver

```sql
SELECT d.first_name, d.last_name, AVG(r.position) AS avg_finish
FROM results r
JOIN drivers d ON r.driver_id = d.driver_id
WHERE r.position IS NOT NULL
GROUP BY d.driver_id
ORDER BY avg_finish ASC;
```

This query helps compare driver consistency across seasons.

---

## Challenges Encountered

- Handling `AUTO_INCREMENT` vs manual IDs
- Foreign key constraint errors when inserting data out of order
- Ensuring race IDs existed before inserting results
- Converting race time formats accurately
- Managing NULL values for DNFs and non-classified drivers
- Avoiding duplicate driver or team records

---

## Future Improvements

- Add sprint race support
- Add qualifying results
- Track fastest laps
- Add constructor championship calculations
- Create SQL views for:
  - Season standings
  - Driver career totals
- Build visual dashboards using a BI tool

---

## Skills Demonstrated

- SQL schema design
- Foreign key relationships
- Data normalization
- Time-based calculations
- Aggregate queries
- Debugging SQL constraint errors
- Real-world sports data modeling

---

## Author Notes

This project was built as a learning-focused SQL portfolio piece, emphasizing clarity, correctness, and realistic data structures rather than automation or scraping.




