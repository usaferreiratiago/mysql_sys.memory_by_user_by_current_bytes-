# mysql_sys.memory_by_user_by_current_bytes-

SELECT 
    IF((`performance_schema`.`memory_summary_by_user_by_event_name`.`USER` IS NULL),
        'background',
        `performance_schema`.`memory_summary_by_user_by_event_name`.`USER`) AS `user`,
    SUM(`performance_schema`.`memory_summary_by_user_by_event_name`.`CURRENT_COUNT_USED`) AS `current_count_used`,
    FORMAT_BYTES(SUM(`performance_schema`.`memory_summary_by_user_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`)) AS `current_allocated`,
    FORMAT_BYTES(IFNULL((SUM(`performance_schema`.`memory_summary_by_user_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`) / NULLIF(SUM(`performance_schema`.`memory_summary_by_user_by_event_name`.`CURRENT_COUNT_USED`),
                            0)),
                    0)) AS `current_avg_alloc`,
    FORMAT_BYTES(MAX(`performance_schema`.`memory_summary_by_user_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`)) AS `current_max_alloc`,
    FORMAT_BYTES(SUM(`performance_schema`.`memory_summary_by_user_by_event_name`.`SUM_NUMBER_OF_BYTES_ALLOC`)) AS `total_allocated`
FROM
    `performance_schema`.`memory_summary_by_user_by_event_name`
GROUP BY IF((`performance_schema`.`memory_summary_by_user_by_event_name`.`USER` IS NULL),
    'background',
    `performance_schema`.`memory_summary_by_user_by_event_name`.`USER`)
ORDER BY SUM(`performance_schema`.`memory_summary_by_user_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`) DESC
