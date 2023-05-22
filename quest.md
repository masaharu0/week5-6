## Step1
テーブル設計

URL：https://www.notion.so/fmasaharu/b379165976bb4a35a089c280effb0d84?pvs=4

## Step2
- データベースを構築します

データベースの作成
```
CREATE DATABASE internet_tv;
```
作成したデータベースに移動
```
use internet_tv;
```
- 各テーブルを作成
```
CREATE TABLE channels (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) UNIQUE
);

CREATE TABLE programs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    description TEXT
);

CREATE TABLE seasons (
    id INT PRIMARY KEY AUTO_INCREMENT,
    program_id INT,
    season INT,
    FOREIGN KEY (program_id) REFERENCES programs(id)
);

CREATE TABLE episodes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    season_id INT,
    title VARCHAR(255),
    description TEXT,
    duration INT,
    air_date DATE,
    episode INT DEFAULT 0,
    FOREIGN KEY (season_id) REFERENCES seasons(id)
);

CREATE TABLE genres (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) UNIQUE
);

CREATE TABLE programs_genres (
    id INT PRIMARY KEY AUTO_INCREMENT,
    genre_id INT,
    program_id INT,
    FOREIGN KEY (genre_id) REFERENCES genres(id),
    FOREIGN KEY (program_id) REFERENCES programs(id)
);

CREATE TABLE channel_programs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    channel_id INT,
    program_id INT,
    start_time DATETIME,
    end_time DATETIME,
    is_repeat BOOL DEFAULT FALSE,
    FOREIGN KEY (channel_id) REFERENCES channels(id),
    FOREIGN KEY (program_id) REFERENCES programs(id)
);

CREATE TABLE view_counts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    channel_program_id INT,
    episode_id INT,
    views INT DEFAULT 0,
    FOREIGN KEY (channel_program_id) REFERENCES channel_programs(id),
    FOREIGN KEY (episode_id) REFERENCES episodes(id)
);
```

- サンプルデータを入れる（適宜追加や変更をしてください）

```
INSERT INTO channels (name)
VALUES ('Channel 1'), ('Channel 2'), ('Channel 3');

INSERT INTO programs (title, description)
VALUES 
('Program 1', 'This is program 1'),
('Program 2', 'This is program 2'),
('Program 3', 'This is program 3');

INSERT INTO seasons (program_id, season)
VALUES 
(1, 1),
(1, 2),
(2, 1),
(3, 1);

INSERT INTO episodes (season_id, title, description, duration, air_date, episode)
VALUES 
(1, 'Episode 1', 'This is episode 1 of season 1', 30, '2023-05-19', 1),
(1, 'Episode 2', 'This is episode 2 of season 1', 30, '2023-05-20', 2),
(2, 'Episode 1', 'This is episode 1 of season 2', 30, '2023-05-21', 1),
(3, 'Episode 1', 'This is episode 1 of program 2', 30, '2023-05-22', 1),
(4, 'Episode 1', 'This is episode 1 of program 3', 30, '2023-05-23', 1);

INSERT INTO genres (name)
VALUES 
('Genre 1'), 
('Genre 2'), 
('Genre 3');

INSERT INTO programs_genres (genre_id, program_id)
VALUES 
(1, 1),
(2, 1),
(3, 2),
(1, 3),
(2, 3);

INSERT INTO channel_programs (channel_id, program_id, start_time, end_time, is_repeat)
VALUES 
(1, 1, '2023-05-19 20:00:00', '2023-05-19 20:30:00', FALSE),
(2, 2, '2023-05-20 20:00:00', '2023-05-20 20:30:00', FALSE),
(3, 3, '2023-05-21 20:00:00', '2023-05-21 20:30:00', FALSE),
(1, 2, '2023-05-22 20:00:00', '2023-05-22 20:30:00', TRUE),
(2, 3, '2023-05-23 20:00:00', '2023-05-23 20:30:00', TRUE);

INSERT INTO view_counts (channel_program_id, episode_id, views)
VALUES 
(1, 1, 1000),
(1, 2, 900),
(2, 3, 1200),
(3, 4, 1100),
(4, 3, 800),
(5, 4, 700);
 

```

## Step3

- よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください
```
SELECT 
    e.title AS episode_title,
    vc.views AS view_count
FROM
    view_counts AS vc
JOIN 
    episodes AS e ON e.id = vc.episode_id
ORDER BY 
    vc.views DESC
LIMIT 3;
```

- よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください
```
SELECT 
    p.title AS program_title,
    s.season AS season_number,
    e.episode AS episode_number,
    e.title AS episode_title,
    vc.views AS view_count
FROM
    view_counts AS vc
JOIN 
    episodes AS e ON e.id = vc.episode_id
JOIN 
    seasons AS s ON s.id = e.season_id
JOIN 
    programs AS p ON p.id = s.program_id
ORDER BY 
    vc.views DESC
LIMIT 3;
```

- 本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします
```
SELECT 
    c.name AS channel_name,
    CONCAT(CURDATE(), ' ', cp.start_time) AS start_time,
    CONCAT(CURDATE(), ' ', cp.end_time) AS end_time,
    s.season AS season_number,
    e.episode AS episode_number,
    e.title AS episode_title,
    e.description AS episode_description
FROM
    channel_programs AS cp
JOIN
    channels AS c ON c.id = cp.channel_id
JOIN
    programs AS p ON p.id = cp.program_id
JOIN
    seasons AS s ON s.program_id = p.id
JOIN
    episodes AS e ON e.season_id = s.id
WHERE
    DATE(e.air_date) = CURDATE()
ORDER BY
    start_time;

```

- 本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください

```
SELECT 
    e.air_date AS date,
    cp.start_time AS start_time,
    cp.end_time AS end_time,
    s.season AS season_number,
    e.episode AS episode_number,
    e.title AS episode_title,
    e.description AS episode_description
FROM 
    channels c
JOIN 
    channel_programs cp ON c.id = cp.channel_id
JOIN 
    programs p ON cp.program_id = p.id
JOIN 
    seasons s ON p.id = s.program_id
JOIN 
    episodes e ON s.id = e.season_id
WHERE 
    c.name = 'channel 1' AND
    e.air_date BETWEEN CURDATE() AND DATE_ADD(CURDATE(), INTERVAL 7 DAY)
ORDER BY 
    date,
    start_time;

```

