--STERGERE TABELE DACA EXISTA
DROP TABLE IF EXISTS rezervari_sejururi CASCADE;
DROP TABLE IF EXISTS incasari_rezervari CASCADE;
DROP TABLE IF EXISTS rezervari_circuite CASCADE;
DROP TABLE IF EXISTS sejururi CASCADE;
DROP TABLE IF EXISTS cazari_circuite CASCADE;
DROP TABLE IF EXISTS transporturi_circuite CASCADE;
DROP TABLE IF EXISTS obiective_turistice_circuite CASCADE;
DROP TABLE IF EXISTS incasari CASCADE;
DROP TABLE IF EXISTS rezervari CASCADE;
DROP TABLE IF EXISTS camere_hotel CASCADE;
DROP TABLE IF EXISTS transporturi CASCADE;
DROP TABLE IF EXISTS circuite_turistice CASCADE;
DROP TABLE IF EXISTS obiective_turistice CASCADE;
DROP TABLE IF EXISTS angajati CASCADE;
DROP TABLE IF EXISTS telefoane_persoane CASCADE;
DROP TABLE IF EXISTS hoteluri CASCADE;
DROP TABLE IF EXISTS contacte_email_parteneri CASCADE;
DROP TABLE IF EXISTS contacte_telefon_parteneri CASCADE;
DROP TABLE IF EXISTS persoane CASCADE;
DROP TABLE IF EXISTS parteneri CASCADE;
DROP TABLE IF EXISTS localitati CASCADE;
DROP TABLE IF EXISTS tari CASCADE;

--CREAREA DE TABELE + PRIMARY KEY + CHEI STRAINE
CREATE TABLE tari (
    idtara NUMERIC(5) PRIMARY KEY,
    numetara VARCHAR(30),
    continent VARCHAR(30),
    populatie NUMERIC(10),
    clima VARCHAR(100),
    limbivorbita VARCHAR(100)
);

CREATE TABLE localitati (
    idlocalitate NUMERIC(10) PRIMARY KEY,
    numelocalitate VARCHAR(70),
    regiune VARCHAR(70),
    idtara NUMERIC(5),
    CONSTRAINT fk_localitati_tari FOREIGN KEY (idtara) REFERENCES tari(idtara)
);

CREATE TABLE persoane (
    idpersoana NUMERIC(10) PRIMARY KEY,
    numepersoana VARCHAR(50),          /*am modificat de la 20 la 50*/
    prenumepersoana VARCHAR(30),
    nationalitate VARCHAR(50),         /*am modificat de la 20 la 50*/
    idlocalitatepersoana NUMERIC(10),
    email_persoana VARCHAR(50),
    CONSTRAINT fk_persoane_localitati FOREIGN KEY (idlocalitatepersoana) REFERENCES localitati(idlocalitate)
);

CREATE TABLE parteneri (
    idpartener NUMERIC(10) PRIMARY KEY,
    numepartener VARCHAR(40),
    adresa_partener VARCHAR(100),
    sitewebpartener VARCHAR(50),
    id_loc_partener NUMERIC(10),
    CONSTRAINT fk_parteneri_localitati FOREIGN KEY (id_loc_partener) REFERENCES localitati(idlocalitate)
);

CREATE TABLE angajati (
    idangajat NUMERIC(10) PRIMARY KEY,
    idpersoana NUMERIC(10),
    departament VARCHAR(50),        /*am modificat de la 20 la 50*/
    functie VARCHAR(25),
    marcasef NUMERIC(2),
    salariu NUMERIC(10),
    CONSTRAINT fk_angajati_persoane FOREIGN KEY (idpersoana) REFERENCES persoane(idpersoana)
);

CREATE TABLE hoteluri (
    idhotel NUMERIC(10) PRIMARY KEY,
    idpartener NUMERIC(10),
    numehotel VARCHAR(50),
    facilitati VARCHAR(250),
    nr_stele INT2,
    cotare VARCHAR(15),
    idlocalitate NUMERIC(10),
    CONSTRAINT fk_hoteluri FOREIGN KEY (idpartener) REFERENCES parteneri(idpartener),
    CONSTRAINT fk_hoteluri2 FOREIGN KEY (idlocalitate) REFERENCES localitati(idlocalitate)      --trb avut grija la denumire FK_... , sa nu fie acelasi nume
);

CREATE TABLE camere_hotel (
    idcamera NUMERIC(10) PRIMARY KEY,
    nrcamera NUMERIC(4),
    idhotel NUMERIC(10),
    tipcamera VARCHAR(100),
    nrpaturi NUMERIC(2),
    pozitionare VARCHAR(100),
    detaliicamera VARCHAR(100),
    CONSTRAINT fk_camere_hotel_hoteluri FOREIGN KEY (idhotel) REFERENCES hoteluri(idhotel)
);

CREATE TABLE transporturi (
    idtransport NUMERIC(10) PRIMARY KEY,
    idpartener NUMERIC(10),
    tiptransport VARCHAR(15),
    modelmijloctransport VARCHAR(50),         /*am modificat de la 20 la 50*/
    detalii VARCHAR(100),
    CONSTRAINT fk_transporturi_parteneri FOREIGN KEY (idpartener) REFERENCES parteneri(idpartener)
);

CREATE TABLE circuite_turistice (
    idcircuitturistic NUMERIC(10) PRIMARY KEY,
    nume_circuit_turistic VARCHAR(500),
    dataplecarii DATE,
    datasosirii DATE,
    tarifcircuit NUMERIC(10),
    descrierecircuit VARCHAR(100),
    nrmijloacetransport NUMERIC(2),
    nrobiectiveturistice NUMERIC(2),
    nrcamererezervate NUMERIC(2)
);

CREATE TABLE obiective_turistice (
    idobiectiv NUMERIC(17) PRIMARY KEY,
    numeobiectiv VARCHAR(50),
    adresaobiectiv VARCHAR(50),
    idlocalitate NUMERIC(10),
    CONSTRAINT fk_obiective_turistice FOREIGN KEY (idlocalitate) REFERENCES localitati(idlocalitate)
);

CREATE TABLE sejururi (
    idsejur NUMERIC(14) PRIMARY KEY,
    idcamera NUMERIC(10),
    idtransport NUMERIC(10),
    dataplecare DATE,
    datasosire DATE,
    nrpersoane NUMERIC(1),
    tarifsejur NUMERIC(10),
    CONSTRAINT fk_sejururi FOREIGN KEY (idcamera) REFERENCES camere_hotel(idcamera),
    CONSTRAINT fk_sejururi2 FOREIGN KEY (idtransport) REFERENCES transporturi(idtransport)
);

CREATE TABLE rezervari (
    idrezervare NUMERIC(14) PRIMARY KEY,
    dataorarezervarii TIMESTAMP,
    codclient NUMERIC(10),
    idpersoanacontact NUMERIC(10),
    suma_totala NUMERIC(14,2),
    discount NUMERIC(10,2),
    CONSTRAINT fk_rezervari FOREIGN KEY (codclient) REFERENCES persoane(idpersoana),
    CONSTRAINT fk_rezervari2 FOREIGN KEY (idpersoanacontact) REFERENCES angajati(idangajat)
);

CREATE TABLE incasari (
    idincasare NUMERIC(15) PRIMARY KEY,
    dataoraincasare TIMESTAMP,
    tipdocincasare VARCHAR(50),            /*am modificat de la 20 la 50*/
    serienrdocincasare NUMERIC(6),
    sumaincasata NUMERIC(14,2)
);

CREATE TABLE rezervari_sejururi (
    idrezervare NUMERIC(14),
    idsejur NUMERIC(14),
    nr_bilete_sejur INT2,
	PRIMARY KEY (idrezervare, idsejur),
    CONSTRAINT fk_rezervari_sejururi FOREIGN KEY (idrezervare) REFERENCES rezervari(idrezervare),
    CONSTRAINT fk_rezervari_sejururi2 FOREIGN KEY (idsejur) REFERENCES sejururi(idsejur)
);

CREATE TABLE incasari_rezervari (
    idincasare NUMERIC(15),
    idrezervare NUMERIC(14),
    transa_rezervare NUMERIC(14,2),
    PRIMARY KEY (idincasare, idrezervare),
    CONSTRAINT fk_incasari_rezervare FOREIGN KEY (idincasare) REFERENCES incasari(idincasare),
    CONSTRAINT fk_incasari_rezervare2 FOREIGN KEY (idrezervare) REFERENCES rezervari(idrezervare)
);

CREATE TABLE rezervari_circuite (
    idrezervare NUMERIC(14),
    idcircuitturistic NUMERIC(10),
    nr_locuri_rezervate INT2,
    PRIMARY KEY (idrezervare, idcircuitturistic),
    CONSTRAINT fk_rezervari_circuite FOREIGN KEY (idrezervare) REFERENCES rezervari(idrezervare),
    CONSTRAINT fk_rezervari_circuite2 FOREIGN KEY (idcircuitturistic) REFERENCES circuite_turistice(idcircuitturistic)
);

CREATE TABLE cazari_circuite (
    idcircuitturistic NUMERIC(10),
    camerarezervatanr NUMERIC(2),
    idcamera NUMERIC(10),
    data_checkin DATE,
    data_checkout DATE,
    PRIMARY KEY (idcircuitturistic, camerarezervatanr),
    CONSTRAINT fk_cazari_circuite FOREIGN KEY (idcircuitturistic) REFERENCES circuite_turistice(idcircuitturistic),
    CONSTRAINT fk_cazari_circuite2 FOREIGN KEY (idcamera) REFERENCES camere_hotel(idcamera)
);

CREATE TABLE transporturi_circuite (
    idcircuitturistic NUMERIC(10),
    mijloctransportnr NUMERIC(2),
    idtransport NUMERIC(10),
    data_ora_inceput TIMESTAMP,
    data_ora_final TIMESTAMP,
    PRIMARY KEY (idcircuitturistic, mijloctransportnr),
    CONSTRAINT fk_transport_circuite FOREIGN KEY (idcircuitturistic) REFERENCES circuite_turistice(idcircuitturistic),
    CONSTRAINT fk_transport_circuite2 FOREIGN KEY (idtransport) REFERENCES transporturi(idtransport)
);

CREATE TABLE obiective_turistice_circuite (
    idcircuitturistic NUMERIC(10),
    obiectivnr NUMERIC(2),
    idobiectiv NUMERIC(17),
    data_ora_inceput TIMESTAMP,
    data_ora_final TIMESTAMP,
    PRIMARY KEY (idcircuitturistic, obiectivnr),
    CONSTRAINT fk_obiective_turistice_circuite FOREIGN KEY (idcircuitturistic) REFERENCES circuite_turistice(idcircuitturistic),
    CONSTRAINT fk_obiective_turistice_circuite2 FOREIGN KEY (idobiectiv) REFERENCES obiective_turistice(idobiectiv)
);

CREATE TABLE telefoane_persoane (
    nrtelefonpersoana VARCHAR(50) PRIMARY KEY,           /*am modificat de la 20 la 50*/
    idpersoana NUMERIC(10),
    CONSTRAINT fk_telefoane_persoane FOREIGN KEY (idpersoana) REFERENCES persoane(idpersoana)
);

CREATE TABLE contacte_email_parteneri (
    emailpartener VARCHAR(50) PRIMARY KEY,
    idpartener NUMERIC(10),
    CONSTRAINT fk_contacte_email_parteneri FOREIGN KEY (idpartener) REFERENCES parteneri(idpartener)
);

CREATE TABLE contacte_telefon_parteneri (
    telefonpartener VARCHAR(50) PRIMARY KEY,             /*am modificat de la 20 la 50*/
    idpartener NUMERIC(10),
    CONSTRAINT fk_telefon_parteneri FOREIGN KEY (idpartener) REFERENCES parteneri(idpartener)
);


--INSERARE INFORMATII IN TABELE
INSERT INTO tari VALUES
(1, 'România', 'Europa', 19000000, 'Temperat', 'Română'),
(2, 'Italia', 'Europa', 60000000, 'Mediterranean', 'Italiană'),
(3, 'Franța', 'Europa', 67000000, 'Oceanic', 'Franceză'),
(4, 'Germania', 'Europa', 83000000, 'Oceanic', 'Germană'),
(5, 'Spania', 'Europa', 47000000, 'Mediterranean', 'Spaniolă'),
(6, 'Grecia', 'Europa', 10700000, 'Mediterranean', 'Greacă'),
(7, 'SUA', 'America de Nord', 330000000, 'Diversă', 'Engleză'),
(8, 'Japonia', 'Asia', 126000000, 'Temperat', 'Japoneză'),
(9, 'Canada', 'America de Nord', 37000000, 'Rece', 'Engleză/Franceză'),
(10, 'Australia', 'Oceania', 25000000, 'Arid', 'Engleză');

INSERT INTO localitati VALUES
(1, 'București', 'Muntenia', 1),
(2, 'Florența', 'Toscana', 2),
(3, 'Paris', 'Île-de-France', 3),
(4, 'Frankfurt', 'Hessa', 4),
(5, 'Almeria', 'Andaluzia', 5),
(6, 'Atena', 'Attica', 6),
(7, 'Texas', 'Texas', 7),
(8, 'Tokyo', 'Kantō', 8),
(9, 'Ottawa', 'Ontario', 9),
(10, 'Canberra', 'Australian Capital Territory', 10);

INSERT INTO persoane VALUES
(1, 'Popescu', 'Ion Mihai', 'Română', 1, 'ion.popescu@email.com'),
(2, 'Ionescu', 'Maria', 'Română', 2, 'maria.ionescu@email.com'),
(3, 'Georgescu', 'Andrei Ștefan', 'Română', 3, 'andrei.geo@email.com'),
(4, 'Dumitrescu', 'Ioana', 'Română', 4, 'ioana.dumi@email.com'),
(5, 'Vasilescu', 'Mihai', 'Română', 5, 'mihai.v@email.com'),
(6, 'Luca', 'Elena Mihaela', 'Română', 1, 'elena.luca@email.com'),
(7, 'Radu', 'Stefan', 'Română', 2, 'stefan.radu@email.com'),
(8, 'Tudor', 'Cristina Ana Mihaela', 'Română', 3, 'cristina.tudor@email.com'),
(9, 'Barbu', 'Florin', 'Română', 4, 'florin.barbu@email.com'),
(10, 'Marin', 'Ana', 'Română', 5, 'ana.marin@email.com');

INSERT INTO parteneri VALUES
(1, 'Partener 1', 'Str. A, nr. 1', 'www.partener1.ro', 1),
(2, 'Partener 2', 'Str. B, nr. 2', 'www.partener2.ro', 2),
(3, 'Partener 3', 'Str. C, nr. 3', 'www.partener3.ro', 3),
(4, 'Partener 4', 'Str. D, nr. 4', 'www.partener4.ro', 4),
(5, 'Partener 5', 'Str. E, nr. 5', 'www.partener5.ro', 5),
(6, 'Partener 6', 'Str. F, nr. 6', 'www.partener6.ro', 6),
(7, 'Partener 7', 'Str. G, nr. 7', 'www.partener7.ro', 7),
(8, 'Partener 8', 'Str. H, nr. 8', 'www.partener8.ro', 8),
(9, 'Partener 9', 'Str. I, nr. 9', 'www.partener9.ro', 9),
(10, 'Partener 10', 'Str. J, nr. 10', 'www.partener10.ro', 10);

INSERT INTO angajati VALUES
(1, 1, 'Vânzări', 'Consultant', 1, 3000),
(2, 2, 'Marketing', 'Analist', 2, 3200),
(3, 3, 'IT', 'Programator', 1, 5000),
(4, 4, 'Resurse Umane', 'Recrutor', 2, 3100),
(5, 5, 'Financiar', 'Contabil', 1, 4000),
(6, 6, 'Turism', 'Agent turism', 3, 2800),
(7, 7, 'Turism', 'Ghid turistic', 2, 2700),
(8, 8, 'Suport', 'Asistent', 4, 2600),
(9, 9, 'Logistică', 'Coordonator', 1, 3500),
(10, 10, 'IT', 'Administrator', 2, 4800);

INSERT INTO hoteluri VALUES
(1, 1, 'Hotel Central', 'WiFi, TV, parcare', 3, 'Standard', 1),
(2, 2, 'Hotel Lux', 'Spa, piscină, mic dejun', 5, 'Premium', 2),
(3, 3, 'Hotel Riviera', 'Plajă, restaurant', 4, 'Exclusive', 5),
(4, 4, 'Hotel Silva', 'Pădure, trasee montane', 3, 'Relax', 3),
(5, 5, 'Hotel Sky', 'Bar, lounge, conferințe', 4, 'Business', 4),
(6, 6, 'Hotel Ocean', 'Plajă privată, piscină', 5, 'Deluxe', 10),
(7, 7, 'Hotel Sunrise', 'Mic dejun, transfer aeroport', 3, 'Standard', 7),
(8, 8, 'Hotel Continental', 'Fitness, room service', 4, 'Business', 6),
(9, 9, 'Hotel Montana', 'Schi, saună', 4, 'Alpin', 8),
(10, 10, 'Hotel Breeze', 'Bungalouri, terasă', 4, 'Exotic', 9);

INSERT INTO camere_hotel VALUES
(1, 101, 1, 'Single', 1, 'Etaj 1', 'Vedere spre oraș'),
(2, 102, 2, 'Double', 2, 'Etaj 2', 'Balcon inclus'),
(3, 103, 3, 'Triple', 3, 'Etaj 3', 'Priveliște mare'),
(4, 104, 4, 'Suite', 4, 'Etaj 4', 'Jacuzzi inclus'),
(5, 105, 5, 'Single', 1, 'Etaj 5', 'Smart TV'),
(6, 106, 6, 'Double', 2, 'Etaj 1', 'Acces rapid la plajă'),
(7, 107, 7, 'Triple', 3, 'Etaj 2', 'Vedere panoramică'),
(8, 108, 8, 'Single', 1, 'Etaj 3', 'Minibar'),
(9, 109, 9, 'Double', 2, 'Etaj 4', 'Acces la spa'),
(10, 110, 10, 'Suite', 4, 'Etaj 5', 'Privat, luxos');

INSERT INTO transporturi VALUES
(1, 1, 'Autocar', 'Mercedes Tourismo', 'Climatizat, Wi-Fi'),
(2, 2, 'Avion', 'Airbus A320', 'Zbor direct, business'),
(3, 3, 'Tren', 'InterCity', 'Cu restaurant'),
(4, 4, 'Autocar', 'Setra S415', 'Spațios, modern'),
(5, 5, 'Avion', 'Boeing 737', 'Low-cost, bagaj inclus'),
(6, 6, 'Vapor', 'Cruiser XL', 'Croazieră 5 stele'),
(7, 7, 'Autocar', 'Volvo 9700', 'Aer condiționat, priză USB'),
(8, 8, 'Tren', 'TGV', 'Viteză mare, confort'),
(9, 9, 'Avion', 'Boeing 787 Dreamliner', 'Premium economy'),
(10, 10, 'Ferry', 'FastFerry', 'Traversare rapidă');

INSERT INTO circuite_turistice VALUES
(1, 'Circuit Transilvania', '2025-06-10', '2025-06-20', 2000, 'Descoperă Transilvania', 2, 3, 5),
(2, 'Marea Mediterană', '2025-07-01', '2025-07-15', 3500, 'Croazieră Mediterană', 1, 5, 6),
(3, 'Europa Centrală', '2025-08-05', '2025-08-20', 3000, 'Capitala Europei', 3, 4, 4),
(4, 'Balcani Istorici', '2025-09-01', '2025-09-10', 1800, 'Istoria balcanică', 1, 3, 3),
(5, 'România Nordică', '2025-09-20', '2025-09-30', 1600, 'Tur rural și cultural', 2, 4, 4),
(6, 'City Break Clasic', '2025-10-05', '2025-10-12', 1400, 'Vizită în capitale europene', 2, 2, 3),
(7, 'Vacanță Grecească', '2025-10-20', '2025-10-28', 2300, 'Relaxare pe plaje grecești', 1, 2, 4),
(8, 'Crăciun în Alpi', '2025-12-20', '2025-12-27', 3200, 'Sărbători la munte', 2, 3, 5),
(9, 'Safari African', '2026-01-10', '2026-01-25', 5000, 'Aventura vieții în Africa', 3, 5, 6),
(10, 'Tur Asia', '2026-02-15', '2026-03-01', 4500, 'Explorare culturală în Asia', 4, 6, 7);

INSERT INTO obiective_turistice VALUES
(1, 'Castelul Bran', 'Str. General Traian Moșoiu, Bran', 1),
(2, 'Catedrala Santa Maria Del Fiore', 'Bd. Piazza del Duomo, București', 2),
(3, 'Muzeul Satului', 'Șos. Kiseleff, București', 1),
(4, 'Turnul Eiffel', 'Champ de Mars, Paris', 6),
(5, 'Colosseum', 'Piazza del Colosseo, Roma', 7),
(6, 'Catedrala din Berlin', 'Am Lustgarten, Berlin', 8),
(7, 'Sagrada Familia', 'Barcelona, Spania', 9),
(8, 'Acropole', 'Atena, Grecia', 10),
(9, 'Palatul Culturii', 'Iași, România', 3),
(10, 'Castelul Peleș', 'Sinaia, România', 1);

INSERT INTO sejururi VALUES
(1, 1, 1, '2024-06-03', '2024-06-01', 2, 1200),
(2, 2, 2, '2025-06-13', '2025-06-10', 2, 1500),
(3, 3, 3, '2024-07-03', '2024-07-01', 3, 1800),
(4, 4, 4, '2025-07-19', '2025-07-15', 2, 1600),
(5, 5, 5, '2024-08-05', '2024-08-01', 4, 2000),
(6, 6, 6, '2024-08-22', '2024-08-20', 2, 1700),
(7, 7, 7, '2025-08-29', '2025-08-25', 3, 1900),
(8, 8, 8, '2024-09-18', '2024-09-15', 2, 1400),
(9, 9, 9, '2025-10-09', '2025-10-01', 2, 1300),
(10, 10, 10, '2025-10-27', '2025-10-20', 4, 2200);

INSERT INTO rezervari VALUES
(1, '2025-05-01 09:00:00', 1, 1, 1300.00, 100.00),
(2, '2025-05-02 10:00:00', 2, 2, 1500.00, 100.00),
(3, '2025-05-03 11:00:00', 3, 3, 1800.00, 120.00),
(4, '2025-05-04 12:00:00', 4, 4, 2000.00, 150.00),
(5, '2025-05-05 13:00:00', 5, 5, 2200.00, 200.00),
(6, '2025-05-06 14:00:00', 6, 6, 2500.00, 100.00),
(7, '2025-05-07 15:00:00', 7, 7, 1600.00, 50.00),
(8, '2025-05-08 16:00:00', 8, 8, 1900.00, 80.00),
(9, '2025-05-09 17:00:00', 9, 9, 2100.00, 90.00),
(10, '2025-05-10 18:00:00', 10, 10, 2300.00, 110.00);

INSERT INTO incasari VALUES
(1, '2025-05-11 10:00:00', 'Factura', 100001, 1300.00),
(2, '2025-05-12 10:00:00', 'Factura', 100002, 1500.00),
(3, '2025-05-13 10:00:00', 'Chitanta', 100003, 1800.00),
(4, '2025-05-14 10:00:00', 'Factura', 100004, 2000.00),
(5, '2025-05-15 10:00:00', 'Chitanta', 100005, 2200.00),
(6, '2025-05-16 10:00:00', 'Factura', 100006, 2500.00),
(7, '2025-05-17 10:00:00', 'Chitanta', 100007, 1600.00),
(8, '2025-05-18 10:00:00', 'Factura', 100008, 1900.00),
(9, '2025-05-19 10:00:00', 'Factura', 100009, 2100.00),
(10, '2025-05-20 10:00:00', 'Factura', 100010, 2300.00);

INSERT INTO rezervari_sejururi VALUES
(1, 1, 2),
(2, 2, 2),
(3, 3, 2),
(4, 4, 2),
(5, 5, 2),
(6, 6, 2),
(7, 7, 2),
(8, 8, 2),
(9, 9, 2),
(10, 10, 2);

INSERT INTO rezervari_circuite VALUES
(1, 1, 2),
(2, 2, 2),
(3, 3, 2),
(4, 4, 2),
(5, 5, 2),
(6, 6, 2),
(7, 7, 2),
(8, 8, 2),
(9, 9, 2),
(10, 10, 2);

INSERT INTO incasari_rezervari VALUES
(1, 1, 1200.00),
(2, 2, 1400.00),
(3, 3, 1600.00),
(4, 4, 1800.00),
(5, 5, 2000.00),
(6, 6, 2300.00),
(7, 7, 1400.00),
(8, 8, 1700.00),
(9, 9, 1900.00),
(10, 10, 2100.00);

INSERT INTO cazari_circuite VALUES
(1, 1, 1, '2025-06-10', '2025-06-12'),
(2, 1, 2, '2025-07-01', '2025-07-03'),
(3, 1, 3, '2025-08-05', '2025-08-07'),
(4, 2, 4, '2025-09-01', '2025-09-03'),
(5, 2, 5, '2025-09-20', '2025-09-22'),
(6, 3, 6, '2025-10-05', '2025-10-07'),
(7, 3, 7, '2025-10-20', '2025-10-22'),
(8, 4, 8, '2025-12-20', '2025-12-22'),
(9, 4, 9, '2026-01-10', '2026-01-12'),
(10, 5, 10, '2026-02-15', '2026-02-17');

INSERT INTO transporturi_circuite VALUES
(1, 1, 1, '2025-06-10 08:00', '2025-06-10 12:00'),
(2, 1, 2, '2025-07-01 09:00', '2025-07-01 13:00'),
(3, 1, 3, '2025-08-05 07:30', '2025-08-05 11:30'),
(4, 2, 4, '2025-09-01 06:00', '2025-09-01 10:00'),
(5, 2, 5, '2025-09-20 10:00', '2025-09-20 14:00'),
(6, 3, 6, '2025-10-05 12:00', '2025-10-05 16:00'),
(7, 3, 7, '2025-10-20 13:00', '2025-10-20 17:00'),
(8, 4, 8, '2025-12-20 09:00', '2025-12-20 13:00'),
(9, 4, 9, '2026-01-10 11:00', '2026-01-10 15:00'),
(10, 5, 10, '2026-02-15 08:30', '2026-02-15 12:30');

INSERT INTO obiective_turistice_circuite VALUES
(1, 1, 1, '2025-06-10 13:00', '2025-06-10 15:00'),
(2, 1, 2, '2025-07-01 14:00', '2025-07-01 16:00'),
(3, 1, 3, '2025-08-05 12:00', '2025-08-05 14:00'),
(4, 2, 4, '2025-09-01 15:00', '2025-09-01 17:00'),
(5, 2, 5, '2025-09-20 14:00', '2025-09-20 16:00'),
(6, 3, 6, '2025-10-05 17:00', '2025-10-05 19:00'),
(7, 3, 7, '2025-10-20 18:00', '2025-10-20 20:00'),
(8, 4, 8, '2025-12-20 14:00', '2025-12-20 16:00'),
(9, 4, 9, '2026-01-10 16:00', '2026-01-10 18:00'),
(10, 5, 10, '2026-02-15 13:00', '2026-02-15 15:00');

INSERT INTO telefoane_persoane VALUES
('0723000001', 1),
('0723000002', 2),
('0723000003', 3),
('0723000004', 4),
('0723000005', 5),
('0723000006', 6),
('0723000007', 7),
('0723000008', 8),
('0723000009', 9),
('0723000010', 10);

INSERT INTO contacte_email_parteneri VALUES
('contact1@partener.ro', 1),
('contact2@partener.ro', 2),
('contact3@partener.ro', 3),
('contact4@partener.ro', 4),
('contact5@partener.ro', 5),
('contact6@partener.ro', 6),
('contact7@partener.ro', 7),
('contact8@partener.ro', 8),
('contact9@partener.ro', 9),
('contact10@partener.ro', 10);

INSERT INTO contacte_telefon_parteneri VALUES
('0310000001', 1),
('0310000002', 2),
('0310000003', 3),
('0310000004', 4),
('0310000005', 5),
('0310000006', 6),
('0310000007', 7),
('0310000008', 8),
('0310000009', 9),
('0310000010', 10);






--EXERCITII TEMA 1

--1.1 Extrageti persoanele cu doua sau mai multe prenume
SELECT  *
FROM persoane
WHERE prenumepersoana LIKE '% %';           /* %=un sir nelimitat de caractere, practic % %= un sir nelim + (spatiu) + un sir nelim*/



--1.2 Afisati sejururile din luna august 2024
SELECT *
FROM sejururi
WHERE EXTRACT(MONTH FROM datasosire) = 8 AND EXTRACT(YEAR FROM datasosire) = 2024;



--1.3 Extrageti obiectivele turistice din localitatea Florenta, Italia
SELECT ot.*                                -- daca lasam doar * la select o sa selecteze toate coloanele din toate tabelele cu care s-a facut join-ul
FROM obiective_turistice ot
INNER JOIN localitati l ON ot.idlocalitate = l.idlocalitate
INNER JOIN tari t ON l.idtara = t.idtara
WHERE l.numelocalitate = 'Florența' AND t.numetara = 'Italia';



--1.4 Care dintre angajati sunt si clienti?
SELECT a.*
FROM angajati a
JOIN persoane p ON a.idpersoana = p.idpersoana;