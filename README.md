Dopo aver creato un nuovo database e aver importato lo schema allegato, eseguite le query del file allegato.
Cosa consegnare? Dopo aver testato le vostre query, riportatele in un file txt e caricatelo nella vostra repo.

# SELECT
1. Selezionare tutte le software house americane (3)

    ```sql
    SELECT *
    FROM software_houses
    WHERE country = 'United States';
    ```

2. Selezionare tutti i giocatori della città di 'Rogahnland' (2)

    ```sql
    SELECT *
    FROM players
    WHERE city = 'Rogahnland';
    ```

3. Selezionare tutti i giocatori il cui nome finisce per "a" (220)

    ```sql
    SELECT *
    FROM players
    WHERE name LIKE '%a';
    ```

4. Selezionare tutte le recensioni scritte dal giocatore con ID = 800 (11)

    ```sql
    SELECT *
    FROM reviews
    WHERE player_id = 800;
    ```

5. Contare quanti tornei ci sono stati nell'anno 2015 (9)

    ```sql
    SELECT COUNT(*)
    FROM tournaments
    WHERE year = 2015;
    ```
6. Selezionare tutti i premi che contengono nella descrizione la parola 'facere' (2)

    ```sql
    SELECT *
    FROM awards
    WHERE description LIKE '%facere%';

    ```

7. Selezionare tutti i videogame che hanno la categoria 2 (FPS) o 6 (RPG), mostrandoli una sola volta (del videogioco vogliamo solo l'ID) (287)

    ```sql
    SELECT DISTINCT videogame_id
    FROM category_videogame
    WHERE category_id IN (2, 6);

    ```

8. Selezionare tutte le recensioni con voto compreso tra 2 e 4 (2947)

    ```sql
    SELECT *
    FROM reviews
    WHERE rating BETWEEN 2 AND 4;

    ```

9. Selezionare tutti i dati dei videogiochi rilasciati nell'anno 2020 (46)

    ```sql
    SELECT *
    FROM videogames
    WHERE YEAR(release_date) = 2020;

    ```

10. Selezionare gli id dei videogame che hanno ricevuto almeno una recensione da 5 stelle, mostrandoli una sola volta (443)

    ```sql
    SELECT DISTINCT videogame_id
    FROM reviews
    WHERE rating = 5;

    ```

***BONUS***

11. Selezionare il numero e la media delle recensioni per il videogioco con ID = 412 (review number = 12, avg_rating = 3.16 circa)

    ```sql
    SELECT 
        COUNT(*) AS review_number,
        AVG(rating) AS avg_rating
    FROM 
        reviews
    WHERE 
        videogame_id = 412;

    ```

12. Selezionare il numero di videogame che la software house con ID = 1 ha rilasciato nel 2018 (13)

    ```sql
    SELECT COUNT(*)
    FROM videogames
    WHERE software_house_id = 1
    AND YEAR(release_date) = 2018;

    ```

------------------------------------------------------------------------------------------------------------------------------------------------------------
# GROUP BY
1. Contare quante software house ci sono per ogni paese (3)

    ```sql
    SELECT country, COUNT(*) AS num_software_houses
    FROM software_houses
    GROUP BY country;

    ```

2. Contare quante recensioni ha ricevuto ogni videogioco (del videogioco vogliamo solo l'ID) (500)

    ```sql
    SELECT videogame_id, COUNT(*) AS num_reviews
    FROM reviews
    GROUP BY videogame_id;

    ```

3. Contare quanti videogiochi hanno ciascuna classificazione PEGI (della classificazione PEGI vogliamo solo l'ID) (13)

    ```sql
    SELECT pegi_label_id, COUNT(*) AS num_videogames
    FROM pegi_label_videogame
    GROUP BY pegi_label_id;

    ```

4. Mostrare il numero di videogiochi rilasciati ogni anno (11)

    ```sql
    SELECT YEAR(release_date) AS year, COUNT(*) AS num_videogames
    FROM videogames
    GROUP BY YEAR(release_date)
    ORDER BY YEAR(release_date);

    ```

5. Contare quanti videogiochi sono disponbiili per ciascun device (del device vogliamo solo l'ID) (7)

    ```sql
    SELECT device_id, COUNT(*) AS num_videogames
    FROM device_videogame
    GROUP BY device_id;

    ```

6. Ordinare i videogame in base alla media delle recensioni (del videogioco vogliamo solo l'ID) (500)

    ```sql
    SELECT videogame_id
    FROM reviews
    GROUP BY videogame_id
    ORDER BY AVG(rating) DESC;   

    ```

------------------------------------------------------------------------------------------------------------------------------------------------------------
# JOIN
1. Selezionare i dati di tutti giocatori che hanno scritto almeno una recensione, mostrandoli una sola volta (996)

    ```sql
    SELECT DISTINCT p.*
    FROM players p
    JOIN reviews r ON p.id = r.player_id;

    ```

2. Sezionare tutti i videogame dei tornei tenuti nel 2016, mostrandoli una sola volta (226)

    ```sql
    SELECT DISTINCT vg.id
    FROM tournaments t
    JOIN tournament_videogame tv ON t.id = tv.tournament_id
    JOIN videogames vg ON tv.videogame_id = vg.id
    WHERE t.year = 2016;

    ```

3. Mostrare le categorie di ogni videogioco (1718)

    ```sql
    SELECT cv.category_id
    FROM category_videogame cv
    INNER JOIN categories c ON cv.category_id = c.id;

    ```

4. Selezionare i dati di tutte le software house che hanno rilasciato almeno un gioco dopo il 2020, mostrandoli una sola volta (6)

    ```sql
    SELECT DISTINCT sh.*
    FROM software_houses sh
    JOIN videogames vg ON sh.id = vg.software_house_id
    WHERE vg.release_date > '2020-01-01';

    ```

5. Selezionare i premi ricevuti da ogni software house per i videogiochi che ha prodotto (55)

    ```sql
    SELECT sh.id AS software_house_id, sh.name AS software_house_name, a.id AS award_id, a.name AS award_name
    FROM software_houses sh
    JOIN videogames vg ON sh.id = vg.software_house_id
    JOIN award_videogame av ON vg.id = av.videogame_id
    JOIN awards a ON av.award_id = a.id;

    ```

6. Selezionare categorie e classificazioni PEGI dei videogiochi che hanno ricevuto recensioni da 4 e 5 stelle, mostrandole una sola volta (3363)

    ```sql
    SELECT DISTINCT category_videogame.videogame_id, 
                    videogames.name AS videogame_name, 
                    categories.name AS category_name, 
                    pegi_labels.name AS pegi_label_name
    FROM videogames
    INNER JOIN category_videogame ON category_videogame.videogame_id = videogames.id 
    INNER JOIN categories ON categories.id = category_videogame.category_id
    INNER JOIN reviews ON reviews.videogame_id = category_videogame.videogame_id
    INNER JOIN pegi_label_videogame ON pegi_label_videogame.videogame_id = category_videogame.videogame_id 
    INNER JOIN pegi_labels ON pegi_labels.id = pegi_label_videogame.pegi_label_id
    WHERE reviews.rating >= 4;

    ```

7. Selezionare quali giochi erano presenti nei tornei nei quali hanno partecipato i giocatori il cui nome inizia per 'S' (474)

    ```sql
    SELECT DISTINCT tournament_videogame.videogame_id
    FROM tournaments
    JOIN player_tournament ON tournaments.id = player_tournament.tournament_id
    JOIN players ON player_tournament.player_id = players.id
    JOIN tournament_videogame ON tournaments.id = tournament_videogame.tournament_id
    WHERE players.name LIKE 'S%';

    ```

8. Selezionare le città in cui è stato giocato il gioco dell'anno del 2018 (36)

    ```sql
    SELECT DISTINCT t.city
    FROM tournaments t
    INNER JOIN tournament_videogame tv ON t.id = tv.tournament_id
    INNER JOIN videogames v ON tv.videogame_id = v.id
    INNER JOIN award_videogame av ON v.id = av.videogame_id
    INNER JOIN awards a ON av.award_id = a.id
    WHERE a.name = 'Gioco dell\'anno'
    AND av.year = 2018;

    ```

9. Selezionare i giocatori che hanno giocato al gioco più atteso del 2018 in un torneo del 2019 (991)

    ```sql
    SELECT DISTINCT p.name, p.lastname, p.nickname
    FROM players p
    INNER JOIN player_tournament pt ON p.id = pt.player_id
    INNER JOIN tournaments t ON pt.tournament_id = t.id
    INNER JOIN tournament_videogame tv ON t.id = tv.tournament_id
    INNER JOIN videogames v ON tv.videogame_id = v.id
    INNER JOIN award_videogame av ON v.id = av.videogame_id
    INNER JOIN awards a ON av.award_id = a.id
    WHERE a.name = 'Gioco più atteso'
    AND av.year = 2018
    AND t.year = 2019;

    ```

# BONUS
10. Selezionare i dati della prima software house che ha rilasciato un gioco, assieme ai dati del gioco stesso (software house id : 5)

    ```sql
    SELECT sh.id AS software_house_id, v.id AS game_id
    FROM software_houses sh
    JOIN videogames v ON sh.id = v.software_house_id
    WHERE v.release_date = (SELECT MIN(release_date) FROM videogames);

    ```

11. Selezionare i dati del videogame (id, name, release_date, totale recensioni) con più recensioni (videogame id : potrebbe uscire 449 o 398, sono entrambi a 20)

    ```sql
    SELECT v.id, v.name, v.release_date, COUNT(r.id) AS total_reviews
    FROM videogames v
    LEFT JOIN reviews r ON v.id = r.videogame_id
    GROUP BY v.id, v.name, v.release_date
    ORDER BY total_reviews DESC
    LIMIT 1;

    ```

12. Selezionare la software house che ha vinto più premi tra il 2015 e il 2016 (software house id : potrebbe uscire 3 o 1, sono entrambi a 3)

    ```sql
    SELECT sh.id, sh.tax_id, sh.city, sh.country, COUNT(av.id) AS total_awards
    FROM software_houses sh
    JOIN videogames v ON sh.id = v.software_house_id
    JOIN award_videogame av ON v.id = av.videogame_id
    WHERE av.year BETWEEN 2015 AND 2016
    GROUP BY sh.id, sh.name, sh.tax_id, sh.city, sh.country
    ORDER BY total_awards DESC
    LIMIT 1;

    ```

13. Selezionare le categorie dei videogame i quali hanno una media recensioni inferiore a 2 (10)

    ```sql
    SELECT c.name AS category_name
    FROM categories c
    INNER JOIN category_videogame cv ON c.id = cv.category_id
    INNER JOIN videogames v ON cv.videogame_id = v.id
    LEFT JOIN reviews r ON v.id = r.videogame_id
    WHERE v.id IN (
        SELECT videogame_id
        FROM reviews
        GROUP BY videogame_id
        HAVING AVG(rating) < 2
    )
    GROUP BY c.id, c.name;

    ```
