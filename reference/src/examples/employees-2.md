Task:

> The company active pursues gender equality.
> Prepare an analysis based on salaries and gender distribution by managers.

```prql
from employees
join salaries [emp_no]
group [emp_no, gender] (
  aggregate [
    emp_salary: average salary
  ]
)
join de:dept_emp [emp_no]
join dm:dept_manager [
  (dm.dept_no = de.dept_no) and s"(de.from_date, de.to_date) OVERLAPS (dm.from_date, dm.to_date)"
]
group [dm.emp_no, gender] (
  aggregate [
    salary_avg: average emp_salary,
    salary_sd: stddev emp_salary
  ]
)
derive [
  mng_no: dm.emp_no
]
join managers:employees [emp_no]
derive [mng_name: s"managers.first_name || ' ' || managers.last_name"]
select [mng_name, managers.gender, salary_avg, salary_sd]
```