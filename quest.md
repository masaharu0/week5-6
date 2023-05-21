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
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE
);

CREATE TABLE programs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT
);

CREATE TABLE seasons (
    id INT AUTO_INCREMENT PRIMARY KEY,
    program_id INT NOT NULL,
    season INT NOT NULL,
    FOREIGN KEY (program_id) REFERENCES programs(id)
);

CREATE TABLE episodes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    season_id INT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    duration INT NOT NULL,
    air_date DATE NOT NULL,
    episode INT,
    FOREIGN KEY (season_id) REFERENCES seasons(id)
);

CREATE TABLE genres (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE
);

CREATE TABLE programs_genres (
    id INT AUTO_INCREMENT PRIMARY KEY,
    genre_id INT NOT NULL,
    program_id INT NOT NULL,
    FOREIGN KEY (genre_id) REFERENCES genres(id),
    FOREIGN KEY (program_id) REFERENCES programs(id)
);

CREATE TABLE channel_programs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    channel_id INT NOT NULL,
    program_id INT NOT NULL,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    FOREIGN KEY (channel_id) REFERENCES channels(id),
    FOREIGN KEY (program_id) REFERENCES programs(id)
);

CREATE TABLE view_counts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    channel_program_id INT NOT NULL,
    episode_id INT NOT NULL,
    views INT DEFAULT 0,
    FOREIGN KEY (channel_program_id) REFERENCES channel_programs(id),
    FOREIGN KEY (episode_id) REFERENCES episodes(id)
);
```

- サンプルデータを入れる

```
INSERT INTO channels (name)
VALUES ('ドラマ1'), ('ドラマ2'), ('アニメ1'), ('アニメ2'), ('スポーツ'), ('ペット');

INSERT INTO programs (title, description)
VALUES 
    ('プログラム1', 'これはプログラム1の説明です'), 
    ('プログラム2', 'これはプログラム2の説明です');

INSERT INTO seasons (program_id, season)
VALUES 
    (1, 1), 
    (1, 2), 
    (2, 1);

INSERT INTO episodes (season_id, title, description, duration, air_date, episode)
VALUES 
    (1, 'エピソード1', 'これはエピソード1の説明です', 30, '2023-05-01', 1),
    (2, 'エピソード2', 'これはエピソード2の説明です', 60, '2023-05-02', 1),
    (2, 'エピソード3', 'これはエピソード3の説明です', 30, '2023-05-03', 2),
    (3, 'エピソード4', 'これはエピソード4の説明です', 60, '2023-05-04', 1);

INSERT INTO genres (name)
VALUES ('アニメ'), ('映画'), ('ドラマ'), ('ニュース');

INSERT INTO programs_genres (genre_id, program_id)
VALUES 
    (1, 1),
    (3, 1),
    (2, 2),
    (4, 2);

INSERT INTO channel_programs (channel_id, program_id, start_time, end_time)
VALUES 
    (1, 1, '08:00:00', '10:00:00'), 
    (1, 2, '10:00:00', '12:00:00'),
    (2, 1, '08:00:00', '10:00:00'), 
    (2, 2, '10:00:00', '12:00:00');

INSERT INTO view_counts (channel_program_id, episode_id, views)
VALUES 
    (1, 1, 5000),
    (2, 2, 6000),
    (3, 3, 7000),
    (4, 4, 8000);  

```

