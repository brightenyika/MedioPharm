# DAX

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

------
## Calendar Dim Table 

**Purpose:**
To create a calender table 

```dax
DateTable =
ADDCOLUMNS(
    CALENDAR(
        MINX(Admissions, Admissions[Admission_date]),
        MAXX(Admissions, Admissions[Discharge_date])
    ),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "Year Month", FORMAT([Date], "YYYY-MM"),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Day", DAY([Date]),
    "Day Name", FORMAT([Date], "DDD"),
    "Week Number", WEEKNUM([Date])
)
```

----
## Response-time analysis

**Purpose:**
Calculates visits within 30 minutes 

```dax
Visits Within 30 Minutes =
CALCULATE(
    [Total Doctor Visits],
    'Doctor''s visit'[Response_time] <= 30
)
```

----
## Visit reason analysis

**Purpose:**
Measures why patients are visiting doctors 

```dax
Visit Reason % =
DIVIDE(
    [Total Doctor Visits],
    CALCULATE(
        [Total Doctor Visits],
        ALL('Doctor''s visit'[Visit_reason])
    ),
    0
)
```

----
## Heart-rate category

**Purpose:**
To categorize heart rate 

```dax
Heart Rate Category =
SWITCH(
    TRUE(),
    Vitals[Heart_rate] < 60, "Low",
    Vitals[Heart_rate] <= 100, "Normal",
    "High"
)
```

------
## Oxygen-rate category

**Purpose:**
To categorize oxygen rate

```dax
Oxygen Category =
SWITCH(
    TRUE(),
    Vitals[Oxygen_saturation] < 90, "Critical",
    Vitals[Oxygen_saturation] < 95, "Low",
    "Normal"
)
```

----
## Temperature category

**Purpose:**
To categorize temperature 

```dax
Temperature Category =
SWITCH(
    TRUE(),
    Vitals[Body_temperature] < 36, "Low",
    Vitals[Body_temperature] <= 37.5, "Normal",
    Vitals[Body_temperature] <= 38, "Elevated",
    "Fever"
)
```

-----

## Patient risk-style analysis

**Purpose:**
To combine vital-sign indicators. 

```dax
Abnormal Vital Count =
VAR HighHR =
    IF(
        AVERAGE(Vitals[Heart_rate]) > 100,
        1,
        0
    )
VAR LowOxygen =
    IF(
        AVERAGE(Vitals[Oxygen_saturation]) < 95,
        1,
        0
    )
VAR HighBP =
    IF(
        AVERAGE(Vitals[Systolic_bp]) >= 140 ||
        AVERAGE(Vitals[Diastolic_bp]) >= 90,
        1,
        0
    )
VAR Fever =
    IF(
        AVERAGE(Vitals[Body_temperature]) > 38,
        1,
        0
    )
RETURN
    HighHR + LowOxygen + HighBP + Fever
```

-----
## Vital Status Indicator 

**Purpose:**
To create vital status indicators 

```dax
SWITCH(
    TRUE(),
    [Abnormal Vital Count] = 0, "Normal",
    [Abnormal Vital Count] = 1, "Monitor",
    [Abnormal Vital Count] >= 2, "Multiple Abnormal Indicators"
)
```

----

## Length-of-stay analysis

**Purpose:**
To identify patients who exceeded 7 days 

```dax
Long Stay Patients =
CALCULATE(
    [Total Admissions],
    Admissions[Length_of_Stay] > 7
)
```

-----

## Readmission analysis

**Purpose:**
To investigate repeat admissions

```dax
Patients With Multiple Admissions =
COUNTROWS(
    FILTER(
        VALUES(Admissions[Patient_id]),
        CALCULATE(COUNTROWS(Admissions)) > 1
    )
)
```

## Readmission percentage

**Purpose:**
To investigate readmissions rate 

```dax
Readmission Patient % =
DIVIDE(
    [Patients With Multiple Admissions],
    [Admitted Patients],
    0
)
```
