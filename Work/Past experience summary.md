# Past experience summary

## Tranglo

- Develop and refactor SP: Kimball style architecture, a snapshot, a merge and update loading script, a main script to track lineage and exceptions

- Refactor:
    - Map column logic and joining
    - Fix wrong metadata tables after discussion
    - Duplicates due to joining: pre-aggregate using CTE
    - SP logic:
        - missing parameter: 5 hour clip window
        - false positives: delete lineage and rerun, will load old 5 hour block but record as new data. fix: use the parameter
        - wrong join sequence: left join causing data to be left out/extra data
        - business contexts: subscription but no wallet

- Document: data mapping, column transformation and join logic

- Users: align with dev team and QA team

  

## Cloud Space

- Ingest data from API endpoint:
    - create API App user
    - use credentials, create token, and send requests with token attached
    - single page and multi page
        - improvement: send small request, check for pagination metadata in response
    - convert different timeformats: Z, +08:00 to BQ compatible datetime, and standardise to UTC+8
  
- Create 3 layers:
    - Raw ingestion
    - Metadata layer:
        - Student list
        - Enrolment:
            - cross join year, term, division, year groups
            - left join student list count + coalesce to account for 0 enrolments for one specific category
        - Attrition masters
        - Year term masters

    - Gold: dashboard specific
        - side to side enrolment comparison
        - attrition rate
        - student lifetime
        - all separated by categories, such as year group, local vs international, reasons for leaving etc

- Challenges:
	- new team from clients
	- back and forth communication:
		- ambiguous business logics
		- study endpoints, documentations, summarise potentially needed endpoints and verify
		- verify business logic calculation