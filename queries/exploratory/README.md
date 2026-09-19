# DAX Queries

## Total Visits

**Purpose:**  
Calculates the number of visits for  tracking.

```dax
Total Visits =
COUNT(doctor_visits[visit_id])
``` 

----

## Total ER Admissions

**Purpose:**
Calculates the toal ER admissions

```dax
Total ER Admissions =
CALCULATE(
   COUNTROWS(admissions),
   admissions[department] = "Emergency"
)
```

---

## Minimum Response Time

**Purpose:**
Calculates the minimum doctor response time 

```dax
Minimum Response Time =
MIN('Doctor''s visit'[Response_time])
```

## Minimum Response Time

**Purpose:**
Calculates the minimum doctor response time 

```dax
Minimum Response Time =
MIN('Doctor''s visit'[Response_time])
