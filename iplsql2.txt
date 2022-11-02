use ipl_proj
select * from dbo.Sheet1$
select * from dbo.iplmatch
select * from ipl_proj.dbo.iplball

-----matches per season
select yr, count(distinct id) as no_of_match from
(select year(date) as yr, id from ipl_proj.dbo.iplmatch)a
group by yr 

-----most player of match
select  player_of_match, count(player_of_match) as coun from ipl_proj.dbo.iplmatch group by player_of_match 

-----most player of season

select * from
(select player_of_match,yr,coun, rank() over(partition by yr order by coun desc) as Rnk from
(select  player_of_match,year(date) as yr, count(player_of_match) as coun from ipl_proj.dbo.iplmatch group by 
player_of_match,year(date) ) a)b where Rnk= 1

---most win by any teams

select  winner, count(winner) as coun from ipl_proj.dbo.iplmatch group by 
winner order by coun desc

----top 5 venue where match is played
select   top 5 venue, count(venue) as coun from ipl_proj.dbo.iplmatch group by 
venue order by coun desc

-----most runs by any batsman

select top 3 batsman, count(total_runs) as coun from ipl_proj.dbo.iplball group by batsman order by coun desc

---% of total runs scored by each batsman

select *, sum(coun) over(order by coun rows between unbounded preceding and unbounded following) as runs from
(select  batsman, sum(total_runs) as coun from ipl_proj.dbo.iplball group by batsman)a

select *, coun/sum(coun) over(order by coun rows between unbounded preceding and unbounded following) as Perc from
(select  batsman, sum(total_runs) as coun from ipl_proj.dbo.iplball group by batsman)a

---most sixes by any batsman

select batsman, count(batsman) as coun from
(select * from  ipl_proj.dbo.iplball where batsman_runs = 6)a
group by batsman order by coun desc

---most sixes by any batsman
select batsman, count(batsman) as coun from
(select * from  ipl_proj.dbo.iplball where batsman_runs = 4)a
group by batsman order by coun desc

---3000 runs club, highest strike rate

select  top 3  batsman, total_runs, strike_rate   from
(select batsman, total_runs, ((total_runs*1)/total_balls)*100 as strike_rate from 
(select  batsman,count(batsman) as total_balls, sum(total_runs) as total_runs from ipl_proj.dbo.iplball group by batsman )a
)b   where total_runs>3000 order by strike_rate desc 

------lowest economy rate for the bowler who has bowled at least 50 overs

select top 3 bowler, (total_runs/(total_balls*1.0)) as economy_rate from
(select bowler, count(bowler) as total_balls, sum(total_runs) as total_runs from ipl_proj.dbo.iplball group by bowler
)a where total_balls>300 order by economy_rate asc

------total no. of matches till 2020
select count(distinct id) from ipl_proj.dbo.iplball

------total no. of matches win by each team
select winner, count(winner) as won from ipl_proj.dbo.iplmatch group by winner order by won desc