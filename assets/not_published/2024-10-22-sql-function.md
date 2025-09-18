tcl




날짜 시간별 분석 문제 모음


select now();
select current_date()
select extract(month from '2024-10-22')
select day('2024-10-22')
select date_add('2024-10-22', interval 7 day)
select date_sub('2024-10-22', interval 7 day)
select datediff('2024-10-22', '2024-10-24')
select timediff('2024-10-22', '2024-10-24')
select date_format(now(), "%Y-%m-%d)

1. dau : daily active user
2020년 7월의 평균 Dau를 구하기. active user 수가 증가하는 추세인가요?

select date_format(visited_at - interval 9 hour, '%Y-%m-%d),
    count(distinct(user_id))
from tbl_visit
where visited_at >= '2020-07-01'
and visited_at <= '2020-07-31'


시간 포맷은 항상 실제 시간을 확인해야한다. 

