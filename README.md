WIP

For more context read the Thesis.pdf 


Below is taken out the thesis

---


### Studielink Sleutelbestanden

According to [Studielink (2023)](#studielink_telbestand2023), the Studielink sleutelbestanden were introduced in 2013 to provide Dutch higher education institutions with detailed, institution-specific enrolment information. Unlike the previously available aggregated and anonymised data (telbestanden), the sleutelbestanden are on the student-level. The data is compiled by Studielink and enriched with information from DUO sources, specifically the 1cijferHO dataset, which indicates the student's history in Dutch Higher Education.

Studielink sleutelbestanden are generated weekly, typically on Mondays, based on a snapshot of the Studielink database that includes changes up to 1:00 AM that day. Each week, a new file is created for every institution, identified by its Brincode. These files contain detailed records for individual applications. See the table below:

#### Table: Raw Studielink Sleutelbestanden

| **Field**         | **Description**                                                                                                                                         |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Brincode          | The institution's unique code.                                                                                                                           |
| Brin_volgnr       | The branch location of the institution where the programme is offered, according to DUO.                                                                 |
| Isatcode          | The programme code, also known as the education code, as defined by DUO.                                                                                 |
| Opl_vorm          | The selected programme form (e.g., full-time, part-time, dual) for which an enrolment request has been submitted.                                       |
| Studiejaar        | The academic year to which the enrolment request pertains, indicated by the first year of the academic year (e.g., 2024 for 2024–2025).                 |
| Maand             | The month in which the student intends to commence their studies, which can be adjusted by the institution.                                              |
| Herkomst          | The student's origin, indicating if they are a Dutch, EEA, non-EEA citizen, or unknown. Determined based on national citizenship codes from DUO.         |
| Geslacht          | The student's gender, based on data from the Studielink database.                                                                                        |
| Meercode_V        | The number of enrolment requests/enrolments a student has (excluding cancelled requests). Includes all statuses except 'A'.                              |
| Meercode_A        | The number of cancelled enrolment requests (status 'Cancelled') per student.                                                                             |
| Status            | The status of the enrolment request: 'V' for request, 'A' for cancellation/rejection, 'U' for withdrawal, 'I' for enrolment.                             |
| Hogerejaars       | Indicates whether the student is starting in a higher academic year. Can be modified by the institution.                                                 |
| Fixus             | Indicates whether a numerus fixus (enrolment cap) applies to the programme.                                                                              |
| Herinschrijving   | Specifies if it is a re-enrolment, based on Studielink checks and DUO's data.                                                                            |
| 1cHO_L            | A code (1–6) from DUO's 1cijferHO-bestand indicating the student's history in Higher Education using a 'long sequence' (mainly for HBO).                 |
| 1cHO_K            | A code (1, 2, 4, 5, or 6) from the 1cijferHO-bestand indicating the student's history in Higher Education using a 'short sequence' (mainly for WO).       |

*Source: [Studielink (2023)](#studielink_telbestand2023)*

---

#### Table: Decision Tree for Determining the 1cHO Value

| **Question**                                                                                         | **Answer** | **Action / Value**        |
|------------------------------------------------------------------------------------------------------|------------|----------------------------|
| Does the student have a registration year (1cijferHO) earlier than the study year (starting October)?| No         | Set value 1cHO = 1         |
|                                                                                                      | Yes        | Next question              |
| Does the student have an active registration in the same type of higher education (HBO/WO)?         | No         | Set value 1cHO = 2         |
|                                                                                                      | Yes        | Next question              |
| Does the student have an active registration in the same level (bachelor/master) within same type?  | No         | Set value 1cHO = 3         |
|                                                                                                      | Yes        | Next question              |
| Does the student have an active registration at the same institution for the same level?            | No         | Set value 1cHO = 4         |
|                                                                                                      | Yes        | Next question              |
| Does the student have an active registration in the same program at the same institution?           | No         | Set value 1cHO = 5         |
|                                                                                                      | Yes        | Set value 1cHO = 6         |

*Source: [Studielink (2023)](#studielink_telbestand2023)*

---

From the overview of fields presented in the raw sleutelbestanden table, two features are particularly worth highlighting: the `Meercode_V` / `Meercode_A` fields and the `1cHO` value.

The `Meercode_V` and `Meercode_A` fields are a useful set of indicators. These values represent the number of enrolment requests a student has submitted (`Meercode_V`) and how many of those were later cancelled (`Meercode_A`) during the application process. A request is counted under `Meercode_V` as long as it holds any status other than 'Cancelled'. If the status changes to 'Cancelled' in a later week, it moves to `Meercode_A` and is no longer included in `Meercode_V`. By comparing these counts to the number of enrolment requests directed at the institution of interest, it is possible to infer whether a student is also considering other universities. This makes the Meercode fields a valuable signal when predicting how serious a student is about actually enrolling with the FEB.

The `1cHO` value is a key indicator that can be used to quickly assess a student's background in Dutch higher education. It shows how familiar a student is within the system compared to their status on October 1st of the previous academic year. Based on the decision tree, the codes have the following meanings:

1. New to Dutch higher education.  
2. Known in Dutch higher education but switched to a different type (e.g., from HBO to WO).  
3. Known in the same type but switched level (e.g., from bachelor to master).  
4. Same type and level, but switched institutions.  
5. Same type, level, and institution, but switched programs.  
6. Same type, level, institution, and program.

The `1cHO` feature will serve as the essential foundation upon which the **StudentType** segmentation will later be based. Further details will be provided in subsection [StudentTypes](#subsubsection-studenttypes).

