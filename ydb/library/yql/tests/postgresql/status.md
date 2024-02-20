==Статус прохождения регрессионных тестов PG в YQL

#|
||№ п/п | Имя теста|Число операторов| Из них выполняется| % выполнения | Последнее обновление | Основные проблемы ||
|| 1 | boolean | 93 | 79 (+6) | 84.95 | 22.01.2024 | YQL-17569 ||
|| 2 | char | 25 | 16 (+13) | 64.0 | 19.01.2024 | YQL-17571 ||
|| 3 | name | 40 | 22 | 55.0 | 29.09.2023 | YQL-17598 ||
|| 4 | varchar | 24 | 15 (+13) | 62.5 | 19.01.2024 | YQL-17603 ||
|| 5 | text | 76 | 16 (+1) | 21.05 | 22.01.2024 | YQL-17605 ||
|| 6 | int2 | 49 | 47 | 95.92 | 29.09.2023 | YQL-17612 ||
|| 7 | int4 | 70 | 68 | 97.14 | 18.02.2024 | YQL-17663 ||
|| 8 | int8 | 142 | 126 | 88.73 | 18.02.2024 | YQL-17614 ||
|| 9 | oid | 27 | 27 (+6) | 100.0 | 22.01.2024 | YQL-17623 ||
|| 10 | float4 | 96 | 82 (+2) | 85.42 | 26.01.2024 | YQL-17586 ||
|| 11 | float8 | 168 | 139 | 82.74 | 18.02.2024 | YQL-17628 ||
|| 12 | bit | 115 | 90 | 78.26 | 18.02.2024 | YQL-17634 ||
|| 13 | numeric | 915 | 813 (+343) | 88.85 | 18.02.2024 | YQL-17629 ||
|| 14 | uuid | 36 | 0 | 0.0 | 02.05.2023 | YQL-17636 ||
|| 15 | strings | 390 | 104 (+1) | 26.67 | 18.02.2024 | YQL-17587 ||
|| 16 | numerology | 24 | 8 | 33.33 | 26.07.2023 |  ||
|| 17 | date | 264 | 202 | 76.52 | 18.02.2024 | YQL-17733 ||
|| 18 | time | 39 | 29 | 74.36 | 30.01.2024 | YQL-17738 ||
|| 19 | timetz | 45 | 19 | 42.22 | 30.01.2024 | YQL-17739 ||
|| 20 | timestamp | 145 | 86 | 59.31 | 18.02.2024 | YQL-17692 ||
|| 21 | timestamptz | 315 | 97 | 30.79 | 30.01.2024 | YQL-17693 ||
|| 22 | interval | 168 | 115 | 68.45 | 25.10.2023 | YQL-17786 ||
|| 23 | horology | 306 | 79 | 25.82 | 10.08.2023 | YQL-17856 ||
|| 24 | comments | 7 | 7 | 100.0 | 25.05.2023 |  ||
|| 25 | expressions | 63 | 14 | 22.22 | 25.10.2023 | YQL-17784 ||
|| 26 | unicode | 13 | 4 | 30.77 | 10.08.2023 | ||
|| 27 | create_table | 368 | 43 | 11.68 | 12.12.2023 | YQL-17664 ||
|| 28 | insert | 357 | 15 | 4.2 | 12.12.2023 | YQL-17785 ||
|| 29 | create_misc | 76 | 3 | 3.95 | 29.09.2023 | YQL-17855 ||
|| 30 | select | 88 | 9 | 10.23 | 12.12.2023 | YQL-17858 ||
|| 31 | select_into | 67 | 3 | 4.48 | 27.07.2023 | YQL-17787 ||
|| 32 | select_distinct | 46 | 1 | 2.17 | 27.07.2023 | YQL-17857 ||
|| 33 | select_distinct_on | 4 | 0 | 0.0 | 25.05.2023 | ||
|| 34 | select_implicit | 44 | 28 (+15) | 63.64 | 30.01.2024 | YQL-17737 ||
|| 35 | select_having | 23 | 19 (+3) | 82.61 | 30.01.2024 | YQL-17736 ||
|| 36 | subselect | 234 | 5 (+3) | 2.14 | 19.01.2024 | YQL-17589 ||
|| 37 | union | 186 | 0 | 0.0 | 25.05.2023 | YQL-17590 ||
|| 38 | case | 63 | 35 (+5) | 55.56 | 18.02.2024 | YQL-17732 ||
|| 39 | join | 591 | 134 (+20) | 22.67 | 18.02.2024 | YQL-17687 ||
|| 40 | aggregates | 416 | 84 (+17) | 20.19 | 18.02.2024 | YQL-17627 ||
|| 41 | arrays | 410 | 108 (+9) | 26.34 | 31.01.2024 | YQL-17707 ||
|| 42 | update | 288 | 23 (+1) | 7.99 | 30.01.2024 | YQL-17685 ||
|| 43 | delete | 10 | 5 (+5) | 50.0 | 27.01.2024 | YQL-17585 ||
|| 44 | dbsize | 24 | 20 | 83.33 | 18.02.2024 | ||
|| 45 | window | 298 | 5 | 1.68 | 12.12.2023 | YQL-17592 ||
|| 46 | functional_deps | 40 | 7 (+1) | 17.5 | 19.01.2024 | ||
|| 47 | json | 450 | 133 (+3) | 29.56 | 18.02.2024 | YQL-17734 ||
|| 48 | jsonb | 1013 | 404 (+3) | 39.88 | 18.02.2024 | YQL-17735 ||
|| 49 | json_encoding | 42 | 42 | 100.0 | 29.05.2023 | ||
|| 50 | jsonpath | 169 | 152 | 89.94 | 29.05.2023 | числа с точкой без целой части (например .1), литерал '00' ||
|| 51 | jsonpath_encoding | 31 | 31 | 100.0 | 29.05.2023 | ||
|| 52 | jsonb_jsonpath | 427 | 88 | 20.61 | 12.12.2023 | ||
|| 53 | limit | 84 | 5 | 5.95 | 10.08.2023 | YQL-17783 ||
|| 54 | truncate | 193 | 41 (+5) | 21.24 | 18.02.2024 | YQL-17740 ||
|| 55 | alter_table | 1679 | 233 (+222) | 13.88 | 18.02.2024 | YQL-17688 ||
|| 56 | xml | 234 | 18 (+4) | 7.69 | 18.02.2024 | YQL-17681 ||
|#