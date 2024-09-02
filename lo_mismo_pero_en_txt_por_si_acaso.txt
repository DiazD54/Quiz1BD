
--Tablas
-- Tabla astronautas
CREATE TABLE astronautas (
    id INT PRIMARY KEY,
    nombre VARCHAR(100),
    apodo VARCHAR(100),
    estatura DECIMAL(5, 2),
    genero CHAR(1),
    sigla_agencia VARCHAR(10)
);

-- Tabla misiones
CREATE TABLE misiones (
    codigo INT PRIMARY KEY,
    objetivo VARCHAR(255),
    valor_mision DECIMAL(15, 2),
    cantidad_astronautas INT,
    fecha_despegue DATE,
    codigo_destino INT,
    sigla_agencia VARCHAR(10)
);

-- Tabla misiones_cohetes
CREATE TABLE misiones_cohetes (
    codigo_mision INT,
    codigo_cohete INT,
    PRIMARY KEY (codigo_mision, codigo_cohete)
);

-- Tabla cohetes
CREATE TABLE cohetes (
    codigo INT PRIMARY KEY,
    nombre VARCHAR(100)
);

-- Tabla destinos
CREATE TABLE destinos (
    codigo INT PRIMARY KEY,
    nombre VARCHAR(100)
);

-- Tabla agencias
CREATE TABLE agencias (
    sigla VARCHAR(10) PRIMARY KEY,
    nombre VARCHAR(100)
);

-- Tabla peleadores
CREATE TABLE peleadores (
    id INT PRIMARY KEY,
    nombre VARCHAR(100),
    apodo VARCHAR(100),
    altura DECIMAL(5, 2)
);

-- Tabla eventos
CREATE TABLE eventos (
    id INT PRIMARY KEY,
    tematica VARCHAR(255),
    fecha DATE,
    id_ubicacion INT
);

-- Tabla eventos_peleadores
CREATE TABLE eventos_peleadores (
    id_evento INT,
    id_peleador1 INT,
    id_peleador2 INT,
    PRIMARY KEY (id_evento, id_peleador1, id_peleador2)
);

-- Tabla ubicaciones
CREATE TABLE ubicaciones (
    id INT PRIMARY KEY,
    nombre VARCHAR(100)
);

-- Tabla apuestas
CREATE TABLE apuestas (
    id INT PRIMARY KEY,
    monto DECIMAL(15, 2)
);

--Inserciones
-- Insertar datos en la tabla astronautas
INSERT INTO astronautas (id, nombre, apodo, estatura, genero, sigla_agencia) VALUES
(1, 'Neil Armstrong', 'Buzz', 1.80, 'M', 'NASA'),
(2, 'Yuri Gagarin', 'Yura', 1.57, 'M', 'RSA'),
(3, 'Valentina Tereshkova', 'Valya', 1.70, 'F', 'RSA');

-- Insertar datos en la tabla misiones
INSERT INTO misiones (codigo, objetivo, valor_mision, cantidad_astronautas, fecha_despegue, codigo_destino, sigla_agencia) VALUES
(1, 'Lunar Landing', 1000000.00, 3, '1969-07-16', 1, 'NASA'),
(2, 'First Human in Space', 500000.00, 1, '1961-04-12', 2, 'RSA');

-- Insertar datos en la tabla misiones_cohetes
INSERT INTO misiones_cohetes (codigo_mision, codigo_cohete) VALUES
(1, 1),
(2, 2);

-- Insertar datos en la tabla cohetes
INSERT INTO cohetes (codigo, nombre) VALUES
(1, 'Saturn V'),
(2, 'Vostok 1');

-- Insertar datos en la tabla destinos
INSERT INTO destinos (codigo, nombre) VALUES
(1, 'Moon'),
(2, 'Low Earth Orbit');

-- Insertar datos en la tabla agencias
INSERT INTO agencias (sigla, nombre) VALUES
('NASA', 'National Aeronautics and Space Administration'),
('RSA', 'Russian Space Agency');

-- Insertar datos en la tabla peleadores
INSERT INTO peleadores (id, nombre, apodo, altura) VALUES
(1, 'Bruce Lee', 'Dragon', 1.72),
(2, 'Chuck Norris', 'Walker', 1.78);

-- Insertar datos en la tabla eventos
INSERT INTO eventos (id, tematica, fecha, id_ubicacion) VALUES
(1, 'Martial Arts Championship', '2023-10-01', 1),
(2, 'Karate Tournament', '2023-11-15', 2);

-- Insertar datos en la tabla eventos_peleadores
INSERT INTO eventos_peleadores (id_evento, id_peleador1, id_peleador2) VALUES
(1, 1, 2),
(2, 1, 2);

-- Insertar datos en la tabla ubicaciones
INSERT INTO ubicaciones (id, nombre) VALUES
(1, 'Madison Square Garden'),
(2, 'Tokyo Dome');

-- Insertar datos en la tabla apuestas
INSERT INTO apuestas (id, monto) VALUES
(1, 1000.00),
(2, 1500.00);

--Punto uno consultas:
--mujer con menor estatura
SELECT *
FROM astronautas
WHERE genero = 'F'
ORDER BY estatura ASC
LIMIT 1;

--Misiones del 2011
SELECT m.objetivo AS nombre_mision, c.nombre AS nombre_cohete, d.nombre AS nombre_destino, m.valor_mision, m.cantidad_astronautas
FROM misiones m
JOIN misiones_cohetes mc ON m.codigo = mc.codigo_mision
JOIN cohetes c ON mc.codigo_cohete = c.codigo
JOIN destinos d ON m.codigo_destino = d.codigo
WHERE m.fecha_despegue BETWEEN '2021-01-01' AND '2021-12-31';

--Astronautas por agencia
SELECT a.sigla_agencia, COUNT(*) AS cantidad_astronautas
FROM astronautas a
GROUP BY a.sigla_agencia;

--Dinero gastado en misiones
SELECT SUM(m.valor_mision) AS total_dinero
FROM misiones m
JOIN agencias ag ON m.sigla_agencia = ag.sigla
WHERE ag.nombre = 'NASA';

--Punto dos consultas:
---Enano menos enano
SELECT nombre, apodo, altura
FROM peleadores
ORDER BY altura DESC
LIMIT 1;

---Mostrar todas las peleas
SELECT e.id AS id_evento, p1.nombre AS peleador1, p2.nombre AS peleador2, e.tematica, e.fecha
FROM eventos_peleadores ep
JOIN peleadores p1 ON ep.id_peleador1 = p1.id
JOIN peleadores p2 ON ep.id_peleador2 = p2.id
JOIN eventos e ON ep.id_evento = e.id;

---Enano que mas participa en peleas
SELECT p.id, COUNT(*) AS num_peleas
FROM peleadores p
JOIN eventos_peleadores ep ON p.id = ep.id_peleador1 OR p.id = ep.id_peleador2
GROUP BY p.id
ORDER BY num_peleas DESC
LIMIT 1;

---Enano que menos participa en peleas
SELECT p.id, COUNT(*) AS num_peleas
FROM peleadores p
JOIN eventos_peleadores ep ON p.id = ep.id_peleador1 OR p.id = ep.id_peleador2
GROUP BY p.id
ORDER BY num_peleas ASC
LIMIT 1;

---Los enanos mas peleones (que mas participan en peleas)
SELECT p.nombre, COUNT(*) AS num_peleas
FROM peleadores p
JOIN eventos_peleadores ep ON p.id = ep.id_peleador1 OR p.id = ep.id_peleador2
GROUP BY p.nombre
ORDER BY num_peleas DESC
LIMIT 3;

---Lugar mas frecuente para las peleas
SELECT u.nombre AS ubicacion, COUNT(*) AS num_peleas
FROM ubicaciones u
JOIN eventos e ON u.id = e.id_ubicacion
GROUP BY u.nombre
ORDER BY num_peleas DESC
LIMIT 1;

---Inverion en apuestas
SELECT SUM(monto) AS total_invertido
FROM apuestas;