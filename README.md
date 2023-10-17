% Nome del database SQLite
dbPath = 'hiv_aids_data.db';

try
    % Crea una connessione al database SQLite
    conn = sqlite(dbPath);

    % Creazione di una tabella "hiv_stats" se non esiste già, includendo regioni e fasce di età
    createTableQuery = ['CREATE TABLE IF NOT EXISTS hiv_stats ('...
        'id INTEGER PRIMARY KEY, '...
        'anno INTEGER, '...
        'paese TEXT, '...
        'regione TEXT, '...
        'sesso TEXT, '...
        'eta TEXT, '...
        'tasso_infezione REAL, '...
        'tasso_mortalita REAL, '...
        'terapia_antiretrovirale INTEGER, '...
        'mezzo_diffusione TEXT, '...
        'trasmissione_verticale INTEGER, '...
        'prevenzione TEXT, '...
        'coinfezione_malattia TEXT, '...
        'sottospecie TEXT)'];

    exec(conn, createTableQuery);

    % Creazione di una tabella "mutazioni_hiv" nel database SQLite
    createMutationTableQuery = ['CREATE TABLE IF NOT EXISTS mutazioni_hiv ('...
        'id INTEGER PRIMARY KEY, '...
        'anno INTEGER, '...
        'mutazione TEXT, '...
        'frequenza REAL)'];

    exec(conn, createMutationTableQuery);

    % Creazione di una tabella "aids_stats" se non esiste già, includendo regioni e fasce di età
    createAidsTableQuery = ['CREATE TABLE IF NOT EXISTS aids_stats ('...
        'id INTEGER PRIMARY KEY, '...
        'anno INTEGER, '...
        'paese TEXT, '...
        'regione TEXT, '...
        'sesso TEXT, '...
        'eta TEXT, '...
        'tasso_infezione REAL, '...
        'tasso_mortalita REAL, '...
        'terapia_antiretrovirale INTEGER, '...
        'mezzo_diffusione TEXT, '...
        'trasmissione_verticale INTEGER, '...
        'prevenzione TEXT, '...
        'coinfezione_malattia TEXT, '...
        'sottospecie TEXT)'];

    exec(conn, createAidsTableQuery);

    % Creazione di una tabella "mutazioni_aids" nel database SQLite
    createMutationAidsTableQuery = ['CREATE TABLE IF NOT EXISTS mutazioni_aids ('...
        'id INTEGER PRIMARY KEY, '...
        'anno INTEGER, '...
        'mutazione TEXT, '...
        'frequenza REAL)'];

    exec(conn, createMutationAidsTableQuery);

    % Definizione di fasce di età, comprese quelle fino a 70 anni
    fasce_eta = {'0-4', '5-9', '10-14', '15-19', '20-24', '25-29', '30-34', '35-39', '40-44', '45-49', '50-54', '55-59', '60-64', '65-69', '70+'};

    % Definizione dei mezzi di diffusione e delle modalità di prevenzione
    mezzi_diffusione = {'Sesso non protetto', 'Uso di droghe', 'Trasmissione verticale', 'Trasfusioni di sangue', 'Altro'};
    modalita_prevenzione = {'Preservativo', 'Test dell''HIV', 'Educazione', 'Terapia antiretrovirale', 'Altro'};

    % Definizione delle malattie di coinfezione
    malattie_coinfezioni = {'Tubercolosi', 'Epatite C', 'Sifilide', 'Malaria', 'Altro'};

    % Definizione delle mutazioni dell'HIV e delle relative frequenze
    mutazioni_hiv = {'M1', 'M2', 'M3', 'M4', 'M5'};
    frequenze_mutazioni_hiv = [0.01, 0.02, 0.015, 0.03, 0.01];

    % Definizione delle mutazioni dell'AIDS e delle relative frequenze
    mutazioni_aids = {'A1', 'A2', 'A3', 'A4', 'A5'};
    frequenze_mutazioni_aids = [0.015, 0.025, 0.02, 0.03, 0.015];

    % Definizione delle sottospecie dell'HIV
    sottospecie_hiv = {'HIV-1', 'HIV-2'};

    % Definizione delle sottospecie dell'AIDS
    sottospecie_aids = {'AIDS-1', 'AIDS-2'};

    % Definizione delle regioni italiane e degli anni
    regioni_italiane = {'Lombardia', 'Lazio', 'Sicilia', 'Campania', 'Piemonte', 'Veneto'};
    anni = 2020:2030;

    % Definizione dei dati di sopravvivenza per ciascuna fascia di età (valori fittizi)
    tasso_sopravvivenza = [0.95, 0.92, 0.88, 0.85, 0.82, 0.79, 0.76, 0.73, 0.70, 0.68, 0.65, 0.62, 0.59, 0.57, 0.55];

    % Inizializza una cella per i colori dei grafici
    colori = {'b', 'g', 'r', 'c', 'm', 'y', 'k'};

    % Esecuzione di cicli per inserire previsioni future casuali e dati di prognosi per l'HIV
    for anno = anni
        for regione_idx = 1:length(regioni_italiane)
            for sesso_idx = 1:length(sessi)
                for eta_idx = 1:length(fasce_eta)
                    for sottospecie_idx = 1:length(sottospecie_hiv)
                        tasso_infezione_previsto = rand * 10; % Valore casuale basato sul tasso di infezione attuale
                        tasso_sopravvivenza_attuale = tasso_sopravvivenza(eta_idx); % Valore di sopravvivenza attuale per questa fascia di età
                        tasso_mortalita_previsto = (1 - tasso_sopravvivenza_attuale) * tasso_infezione_previsto; % Calcolo del tasso di mortalità previsto
                        terapia_antiretrovirale = randi([0, 1], 1, 1); % 0 o 1 persone in terapia antiretrovirale
                        mezzo_diffusione = mezzi_diffusione{randi([1, length(mezzi_diffusione)], 1)}; % Mezzo di diffusione casuale
                        trasmissione_verticale = randi([0, 1], 1, 1); % 0 o 1 trasmissione verticale
                        prevenzione = modalita_prevenzione{randi([1, length(modalita_prevenzione)], 1)}; % Modalità di prevenzione casuale
                        coinfezione_malattia = malattie_coinfezioni{randi([1, length(malattie_coinfezioni)], 1)}; % Malattia di coinfezione casuale

                        % Inserisci dati relativi alle sottospecie dell'HIV
                        sottospecie = sottospecie_hiv{sottospecie_idx};

                        insertDataQuery = 'INSERT INTO hiv_stats (anno, paese, regione, sesso, eta, tasso_infezione, tasso_mortalita, terapia_antiretrovirale, mezzo_diffusione, trasmissione_verticale, prevenzione, coinfezione_malattia, sottospecie) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)';
                        exec(conn, insertDataQuery, [anno, 'Italia', regioni_italiane{regione_idx}, sessi{sesso_idx}, fasce_eta{eta_idx}, tasso_infezione_previsto, tasso_mortalita_previsto, terapia_antiretrovirale, mezzo_diffusione, trasmissione_verticale, prevenzione, coinfezione_malattia, sottospecie]);
                    end
                end
            end
        end
    end

    % Esecuzione di cicli per inserire dati sulle mutazioni per l'HIV
    for anno = anni
        for mutazione_idx = 1:length(mutazioni_hiv)
            frequenza_mutazione = frequenze_mutazioni_hiv(mutazione_idx) + randn(1, 1) * 0.005; % Frequenza casuale con piccola variazione
            insertMutationDataQuery = 'INSERT INTO mutazioni_hiv (anno, mutazione, frequenza) VALUES (?, ?, ?)';
            exec(conn, insertMutationDataQuery, [anno, mutazioni_hiv{mutazione_idx}, frequenza_mutazione]);
        end
    end

    % Esecuzione di cicli per ottenere e generare grafici per il tasso di infezione, mortalità e frequenza delle mutazioni previste per l'HIV
    for eta_idx = 1:length(fasce_eta)
        for sottospecie_idx = 1:length(sottospecie_hiv)
            % Esegui query per ottenere il tasso di infezione e mortalità previsto per la fascia di età corrente
            selectQuery = 'SELECT anno, AVG(tasso_infezione) AS tasso_medio_infezione, AVG(tasso_mortalita) AS tasso_medio_mortalita FROM hiv_stats WHERE eta = ? AND sottospecie = ? GROUP BY anno';
            data_query = fetch(conn, selectQuery, {fasce_eta{eta_idx}, sottospecie_hiv{sottospecie_idx}});

            % Estrai colonne di dati
            anni = data_query.anno;
            tasso_medio_infezione = data_query.tasso_medio_infezione;
            tasso_medio_mortalita = data_query.tasso_medio_mortalita;

            % Crea un nuovo grafico per il tasso medio di infezione e mortalità previsto per questa fascia di età e sottospecie
            figure;
            subplot(3, 1, 1);
            plot(anni, tasso_medio_infezione, '-o', 'Color', colori{eta_idx});
            title(['Tasso medio di infezione previsto per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di infezione');
            grid on;

            subplot(3, 1, 2);
            plot(anni, tasso_medio_mortalita, '-o', 'Color', colori{eta_idx});
            title(['Tasso medio di mortalità previsto per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di mortalità');
            grid on;

            % Esegui query per ottenere la frequenza delle mutazioni previste per la fascia di età e sottospecie correnti
            selectMutationQuery = 'SELECT anno, mutazione, AVG(frequenza) AS frequenza_media FROM mutazioni_hiv WHERE anno <= ? GROUP BY anno, mutazione';
            mutation_data_query = fetch(conn, selectMutationQuery, max(anni));

            % Estrai colonne di dati
            anni_mutazioni = mutation_data_query.anno;
            mutazioni = mutation_data_query.mutazione;
            frequenze_medie = mutation_data_query.frequenza_media;

            % Crea un nuovo grafico per la frequenza media delle mutazioni previste
            subplot(3, 1, 3);
            hold on;
            for mutazione_idx = 1:length(mutazioni_hiv)
                mutazione_corrente = mutazioni_hiv{mutazione_idx};
                plot(anni_mutazioni(mutazioni == mutazione_corrente), frequenze_medie(mutazioni == mutazione_corrente), '-o', 'Color', colori{mutazione_idx}, 'DisplayName', ['Mutazione ', mutazione_corrente]);
            end
            title(['Frequenza media delle mutazioni previste per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Frequenza media');
            legend('Location', 'Best');
            grid on;

            hold off;
        end
    end

    % Esecuzione di cicli per ottenere e generare grafici per il tasso di infezione e mortalità per le varie regioni italiane per l'HIV
    for regione_idx = 1:length(regioni_italiane)
        for sottospecie_idx = 1:length(sottospecie_hiv)
            % Esegui query per ottenere il tasso di infezione e mortalità previsto per la regione corrente
            selectRegionQuery = 'SELECT anno, AVG(tasso_infezione) AS tasso_medio_infezione, AVG(tasso_mortalita) AS tasso_medio_mortalita FROM hiv_stats WHERE regione = ? AND sottospecie = ? GROUP BY anno';
            region_data_query = fetch(conn, selectRegionQuery, {regioni_italiane{regione_idx}, sottospecie_hiv{sottospecie_idx}});

            % Estrai colonne di dati
            anni_region = region_data_query.anno;
            tasso_medio_infezione_region = region_data_query.tasso_medio_infezione;
            tasso_medio_mortalita_region = region_data_query.tasso_medio_mortalita;

            % Crea un nuovo grafico per il tasso medio di infezione e mortalità previsto per la regione corrente
            figure;
            subplot(2, 1, 1);
            plot(anni_region, tasso_medio_infezione_region, '-o', 'Color', 'b');
            title(['Tasso medio di infezione previsto per la regione: ', regioni_italiane{regione_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di infezione');
            grid on;

            subplot(2, 1, 2);
            plot(anni_region, tasso_medio_mortalita_region, '-o', 'Color', 'r');
            title(['Tasso medio di mortalità previsto per la regione: ', regioni_italiane{regione_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio de mortalità');
            grid on;
        end
    end

    % Esecuzione di cicli per ottenere e generare grafici per il tasso di infezione e mortalità per le varie fasce di età per l'HIV
    for eta_idx = 1:length(fasce_eta)
        for sottospecie_idx = 1:length(sottospecie_hiv)
            % Esegui query per ottenere il tasso di infezione e mortalità previsto per la fascia di età corrente
            selectEtaQuery = 'SELECT anno, AVG(tasso_infezione) AS tasso_medio_infezione, AVG(tasso_mortalita) AS tasso_medio_mortalita FROM hiv_stats WHERE eta = ? AND sottospecie = ? GROUP BY anno';
            eta_data_query = fetch(conn, selectEtaQuery, {fasce_eta{eta_idx}, sottospecie_hiv{sottospecie_idx}});

            % Estrai colonne di dati
            anni_eta = eta_data_query.anno;
            tasso_medio_infezione_eta = eta_data_query.tasso_medio_infezione;
            tasso_medio_mortalita_eta = eta_data_query.tasso_medio_mortalita;

            % Crea un nuovo grafico per il tasso medio di infezione e mortalità previsto per la fascia di età corrente
            figure;
            subplot(2, 1, 1);
            plot(anni_eta, tasso_medio_infezione_eta, '-o', 'Color', 'b');
            title(['Tasso medio di infezione previsto per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di infezione');
            grid on;

            subplot(2, 1, 2);
            plot(anni_eta, tasso_medio_mortalita_eta, '-o', 'Color', 'r');
            title(['Tasso medio di mortalità previsto per la fascia de età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_hiv{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio de mortalità');
            grid on;
        end
    end

    % Esecuzione di cicli per inserire previsioni future casuali e dati di prognosi per l'AIDS
    for anno = anni
        for regione_idx = 1:length(regioni_italiane)
            for sesso_idx = 1:length(sessi)
                for eta_idx = 1:length(fasce_eta)
                    for sottospecie_idx = 1:length(sottospecie_aids)
                        tasso_infezione_previsto = rand * 5; % Valore casuale basato sul tasso di infezione attuale
                        tasso_sopravvivenza_attuale = tasso_sopravvivenza(eta_idx); % Valore di sopravvivenza attuale per questa fascia di età
                        tasso_mortalita_previsto = (1 - tasso_sopravvivenza_attuale) * tasso_infezione_previsto; % Calcolo del tasso di mortalità previsto
                        terapia_antiretrovirale = randi([0, 1], 1, 1); % 0 o 1 persone in terapia antiretrovirale
                        mezzo_diffusione = mezzi_diffusione{randi([1, length(mezzi_diffusione)], 1)}; % Mezzo di diffusione casuale
                        trasmissione_verticale = randi([0, 1], 1, 1); % 0 o 1 trasmissione verticale
                        prevenzione = modalita_prevenzione{randi([1, length(modalita_prevenzione)], 1)}; % Modalità di prevenzione casuale
                        coinfezione_malattia = malattie_coinfezioni{randi([1, length(malattie_coinfezioni)], 1)}; % Malattia di coinfezione casuale

                        % Inserisci dati relativi alle sottospecie dell'AIDS
                        sottospecie = sottospecie_aids{sottospecie_idx};

                        insertDataQuery = 'INSERT INTO aids_stats (anno, paese, regione, sesso, eta, tasso_infezione, tasso_mortalita, terapia_antiretrovirale, mezzo_diffusione, trasmissione_verticale, prevenzione, coinfezione_malattia, sottospecie) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)';
                        exec(conn, insertDataQuery, [anno, 'Italia', regioni_italiane{regione_idx}, sessi{sesso_idx}, fasce_eta{eta_idx}, tasso_infezione_previsto, tasso_mortalita_previsto, terapia_antiretrovirale, mezzo_diffusione, trasmissione_verticale, prevenzione, coinfezione_malattia, sottospecie]);
                    end
                end
            end
        end
    end

    % Esecuzione di cicli per inserire dati sulle mutazioni per l'AIDS
    for anno = anni
        for mutazione_idx = 1:length(mutazioni_aids)
            frequenza_mutazione = frequenze_mutazioni_aids(mutazione_idx) + randn(1, 1) * 0.005; % Frequenza casuale con piccola variazione
            insertMutationDataQuery = 'INSERT INTO mutazioni_aids (anno, mutazione, frequenza) VALUES (?, ?, ?)';
            exec(conn, insertMutationDataQuery, [anno, mutazioni_aids{mutazione_idx}, frequenza_mutazione]);
        end
    end

    % Esecuzione di cicli per ottenere e generare grafici per il tasso di infezione, mortalità e frequenza delle mutazioni previste per l'AIDS
    for eta_idx = 1:length(fasce_eta)
        for sottospecie_idx = 1:length(sottospecie_aids)
            % Esegui query per ottenere il tasso di infezione e mortalità previsto per la fascia di età corrente
            selectQuery = 'SELECT anno, AVG(tasso_infezione) AS tasso_medio_infezione, AVG(tasso_mortalita) AS tasso_medio_mortalita FROM aids_stats WHERE eta = ? AND sottospecie = ? GROUP BY anno';
            data_query = fetch(conn, selectQuery, {fasce_eta{eta_idx}, sottospecie_aids{sottospecie_idx}});

            % Estrai colonne di dati
            anni = data_query.anno;
            tasso_medio_infezione = data_query.tasso_medio_infezione;
            tasso_medio_mortalita = data_query.tasso_medio_mortalita;

            % Crea un nuovo grafico per il tasso medio di infezione e mortalità previsto per questa fascia di età e sottospecie
            figure;
            subplot(3, 1, 1);
            plot(anni, tasso_medio_infezione, '-o', 'Color', colori{eta_idx});
            title(['Tasso medio di infezione previsto per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di infezione');
            grid on;

            subplot(3, 1, 2);
            plot(anni, tasso_medio_mortalita, '-o', 'Color', colori{eta_idx});
            title(['Tasso medio di mortalità previsto per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di mortalità');
            grid on;

            % Esegui query per ottenere la frequenza delle mutazioni previste per la fascia di età e sottospecie correnti
            selectMutationQuery = 'SELECT anno, mutazione, AVG(frequenza) AS frequenza_media FROM mutazioni_aids WHERE anno <= ? GROUP BY anno, mutazione';
            mutation_data_query = fetch(conn, selectMutationQuery, max(anni));

            % Estrai colonne di dati
            anni_mutazioni = mutation_data_query.anno;
            mutazioni = mutation_data_query.mutazione;
            frequenze_medie = mutation_data_query.frequenza_media;

            % Crea un nuovo grafico per la frequenza media delle mutazioni previste
            subplot(3, 1, 3);
            hold on;
            for mutazione_idx = 1:length(mutazioni_aids)
                mutazione_corrente = mutazioni_aids{mutazione_idx};
                plot(anni_mutazioni(mutazioni == mutazione_corrente), frequenze_medie(mutazioni == mutazione_corrente), '-o', 'Color', colori{mutazione_idx}, 'DisplayName', ['Mutazione ', mutazione_corrente]);
            end
            title(['Frequenza media delle mutazioni previste per la fascia di età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Frequenza media');
            legend('Location', 'Best');
            grid on;

            hold off;
        end
    end

    % Esecuzione di cicli per ottenere e generare grafici per il tasso di infezione e mortalità per le varie regioni italiane per l'AIDS
    for regione_idx = 1:length(regioni_italiane)
        for sottospecie_idx = 1:length(sottospecie_aids)
            % Esegui query per ottenere il tasso di infezione e mortalità previsto per la regione corrente
            selectRegionQuery = 'SELECT anno, AVG(tasso_infezione) AS tasso_medio_infezione, AVG(tasso_mortalita) AS tasso_medio_mortalita FROM aids_stats WHERE regione = ? AND sottospecie = ? GROUP BY anno';
            region_data_query = fetch(conn, selectRegionQuery, {regioni_italiane{regione_idx}, sottospecie_aids{sottospecie_idx}});

            % Estrai colonne di dati
            anni_region = region_data_query.anno;
            tasso_medio_infezione_region = region_data_query.tasso_medio_infezione;
            tasso_medio_mortalita_region = region_data_query.tasso_medio_mortalita;

            % Crea un nuovo grafico per il tasso medio di infezione e mortalità previsto per la regione corrente
            figure;
            subplot(2, 1, 1);
            plot(anni_region, tasso_medio_infezione_region, '-o', 'Color', 'b');
            title(['Tasso medio di infezione previsto per la regione: ', regioni_italiane{regione_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di infezione');
            grid on;

            subplot(2, 1, 2);
            plot(anni_region, tasso_medio_mortalita_region, '-o', 'Color', 'r');
            title(['Tasso medio di mortalità previsto per la regione: ', regioni_italiane{regione_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio de mortalità');
            grid on;
        end
    end

    % Esecuzione di cicli per ottenere e generare grafici per il tasso di infezione e mortalità per le varie fasce di età per l'AIDS
    for eta_idx = 1:length(fasce_eta)
        for sottospecie_idx = 1:length(sottospecie_aids)
            % Esegui query per ottenere il tasso di infezione e mortalità previsto per la fascia de età corrente
            selectEtaQuery = 'SELECT anno, AVG(tasso_infezione) AS tasso_medio_infezione, AVG(tasso_mortalita) AS tasso_medio_mortalita FROM aids_stats WHERE eta = ? AND sottospecie = ? GROUP BY anno';
            eta_data_query = fetch(conn, selectEtaQuery, {fasce_eta{eta_idx}, sottospecie_aids{sottospecie_idx}});

            % Estrai colonne di dati
            anni_eta = eta_data_query.anno;
            tasso_medio_infezione_eta = eta_data_query.tasso_medio_infezione;
            tasso_medio_mortalita_eta = eta_data_query.tasso_medio_mortalita;

            % Crea un nuovo grafico per il tasso medio de infezione e mortalità previsto per la fascia de età corrente
            figure;
            subplot(2, 1, 1);
            plot(anni_eta, tasso_medio_infezione_eta, '-o', 'Color', 'b');
            title(['Tasso medio di infezione previsto per la fascia de età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio di infezione');
            grid on;

            subplot(2, 1, 2);
            plot(anni_eta, tasso_medio_mortalita_eta, '-o', 'Color', 'r');
            title(['Tasso medio de mortalità previsto per la fascia de età: ', fasce_eta{eta_idx}, ', Sottospecie: ', sottospecie_aids{sottospecie_idx}]);
            xlabel('Anno');
            ylabel('Tasso medio de mortalità');
            grid on;
        end
    end

    % Chiudi la connessione al database
    close(conn);

catch e
    % In caso di errore, visualizza un messaggio di errore
    disp(['Errore: ', e.message]);
    if exist('conn', 'var')
        close(conn);
    end
end
