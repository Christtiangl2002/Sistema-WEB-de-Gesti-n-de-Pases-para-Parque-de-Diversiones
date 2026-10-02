# Sistema de Gestión de Pases para Parque de Atracciones

## Descripción

Este proyecto consiste en el desarrollo de un sistema web para la gestión de clientes y pases de un parque de atracciones.

El sistema está diseñado para permitir el registro y administración de clientes, la compra de diferentes tipos de pases, el registro de pagos, la emisión y consulta de pases, así como el seguimiento del historial de actualización de los mismos. También contempla el manejo de temporadas y diferentes modalidades de pases, incluyendo pases anuales, pases de un día y pases complementarios.

La base de datos fue diseñada para organizar y relacionar la información necesaria para gestionar los procesos de clientes, compras, pagos y pases de manera estructurada.

## Diagrama Entidad-Relación

A continuación se muestra el Diagrama Entidad-Relación (DER) de la base de datos:

![Diagrama Entidad-Relación](DER.png)

## Base de Datos

La base de datos utilizada para el proyecto se denomina `parque_atracciones` y está compuesta por las siguientes entidades:

* `cliente`
* `temporada`
* `tipo_pase`
* `compra`
* `detalle_compra`
* `pago`
* `pase`
* `historial_pase`

### Código SQL

```sql
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema parque_atracciones
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema parque_atracciones
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `parque_atracciones` DEFAULT CHARACTER SET utf8 ;
USE `parque_atracciones` ;

-- -----------------------------------------------------
-- Table `parque_atracciones`.`cliente`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`cliente` (
  `idcliente` INT NOT NULL AUTO_INCREMENT,
  `numero_cliente` VARCHAR(20) NOT NULL,
  `nombre` VARCHAR(50) NOT NULL,
  `apellidos` VARCHAR(100) NOT NULL,
  `fecha_nacimiento` DATE NOT NULL,
  `correo` VARCHAR(255) NOT NULL,
  `telefono` VARCHAR(15) NOT NULL,
  `password` VARCHAR(255) NOT NULL,
  `fecha_registro` DATETIME NOT NULL,
  UNIQUE INDEX `numero_UNIQUE` (`numero_cliente` ASC) VISIBLE,
  UNIQUE INDEX `correo_UNIQUE` (`correo` ASC) VISIBLE,
  PRIMARY KEY (`idcliente`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`temporada`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`temporada` (
  `id_temporada` INT NOT NULL AUTO_INCREMENT,
  `nombre` VARCHAR(50) NOT NULL,
  `fecha_inicio` DATE NOT NULL,
  `fecha_fin` DATE NOT NULL,
  `estado` VARCHAR(20) NOT NULL,
  PRIMARY KEY (`id_temporada`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`tipo_pase`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`tipo_pase` (
  `id_tipo_pase` INT NOT NULL AUTO_INCREMENT,
  `nombre` VARCHAR(50) NOT NULL,
  `modalidad` VARCHAR(20) NOT NULL,
  `precio` DECIMAL(10,2) NOT NULL,
  `nivel` INT NULL,
  `categoria` VARCHAR(20) NOT NULL,
  PRIMARY KEY (`id_tipo_pase`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`compra`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`compra` (
  `id_compra` INT NOT NULL AUTO_INCREMENT,
  `id_cliente` INT NOT NULL,
  `fecha_compra` DATETIME NOT NULL,
  `total` DECIMAL(10,2) NOT NULL,
  `estado` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`id_compra`),
  INDEX `id_cliente_idx` (`id_cliente` ASC) VISIBLE,
  CONSTRAINT `fk_compra_cliente`
    FOREIGN KEY (`id_cliente`)
    REFERENCES `parque_atracciones`.`cliente` (`idcliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`detalle_compra`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`detalle_compra` (
  `id_detalle` INT NOT NULL AUTO_INCREMENT,
  `id_compra` INT NOT NULL,
  `id_tipo_pase` INT NOT NULL,
  `cantidad` INT NOT NULL,
  `precio_unitario` DECIMAL(10,2) NOT NULL,
  `subtotal` DECIMAL(10,2) NOT NULL,
  PRIMARY KEY (`id_detalle`),
  INDEX `id_compra_idx` (`id_compra` ASC) VISIBLE,
  INDEX `id_tipo_pase_idx` (`id_tipo_pase` ASC) VISIBLE,
  CONSTRAINT `fk_detalle_compra_compra`
    FOREIGN KEY (`id_compra`)
    REFERENCES `parque_atracciones`.`compra` (`id_compra`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_detalle_compra_tipo_pase`
    FOREIGN KEY (`id_tipo_pase`)
    REFERENCES `parque_atracciones`.`tipo_pase` (`id_tipo_pase`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`pago`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`pago` (
  `id_pago` INT NOT NULL AUTO_INCREMENT,
  `id_compra` INT NOT NULL,
  `metodo_pago` VARCHAR(45) NOT NULL,
  `estado` VARCHAR(45) NOT NULL,
  `monto` DECIMAL(10,2) NOT NULL,
  `fecha_pago` DATETIME NOT NULL,
  PRIMARY KEY (`id_pago`),
  UNIQUE INDEX `id_compra_UNIQUE` (`id_compra` ASC) VISIBLE,
  CONSTRAINT `fk_pago_compra`
    FOREIGN KEY (`id_compra`)
    REFERENCES `parque_atracciones`.`compra` (`id_compra`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`pase`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`pase` (
  `id_pase` INT NOT NULL AUTO_INCREMENT,
  `numero_pase` VARCHAR(45) NOT NULL,
  `id_cliente` INT NOT NULL,
  `id_detalle_compra` INT NOT NULL,
  `id_tipo_pase` INT NOT NULL,
  `id_temporada` INT NULL,
  `fecha_compra` DATETIME NOT NULL,
  `fecha_inicio` DATE NULL,
  `fecha_fin` DATE NULL,
  `fecha_uso` DATE NULL,
  `estado` VARCHAR(20) NOT NULL,
  PRIMARY KEY (`id_pase`),
  UNIQUE INDEX `numero_pase_UNIQUE` (`numero_pase` ASC) VISIBLE,
  INDEX `id_cliente_idx` (`id_cliente` ASC) VISIBLE,
  INDEX `id_detalle_compra_idx` (`id_detalle_compra` ASC) VISIBLE,
  INDEX `id_tipo_pase_idx` (`id_tipo_pase` ASC) VISIBLE,
  INDEX `id_temporada_idx` (`id_temporada` ASC) VISIBLE,
  CONSTRAINT `fk_pase_cliente`
    FOREIGN KEY (`id_cliente`)
    REFERENCES `parque_atracciones`.`cliente` (`idcliente`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_pase_detalle_compra`
    FOREIGN KEY (`id_detalle_compra`)
    REFERENCES `parque_atracciones`.`detalle_compra` (`id_detalle`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_pase_tipo_pase`
    FOREIGN KEY (`id_tipo_pase`)
    REFERENCES `parque_atracciones`.`tipo_pase` (`id_tipo_pase`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_pase_temporada`
    FOREIGN KEY (`id_temporada`)
    REFERENCES `parque_atracciones`.`temporada` (`id_temporada`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `parque_atracciones`.`historial_pase`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `parque_atracciones`.`historial_pase` (
  `id_historial` INT NOT NULL AUTO_INCREMENT,
  `id_pase` INT NOT NULL,
  `id_tipo_pase` INT NOT NULL,
  `id_compra` INT NULL,
  `fecha` DATETIME NOT NULL,
  `tipo_movimiento` VARCHAR(20) NOT NULL,
  PRIMARY KEY (`id_historial`),
  INDEX `id_pase_idx` (`id_pase` ASC) VISIBLE,
  INDEX `id_tipo_pase_idx` (`id_tipo_pase` ASC) VISIBLE,
  INDEX `id_compra_idx` (`id_compra` ASC) VISIBLE,
  CONSTRAINT `fk_historial_pase_pase`
    FOREIGN KEY (`id_pase`)
    REFERENCES `parque_atracciones`.`pase` (`id_pase`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_historial_pase_tipo_pase`
    FOREIGN KEY (`id_tipo_pase`)
    REFERENCES `parque_atracciones`.`tipo_pase` (`id_tipo_pase`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_historial_pase_compra`
    FOREIGN KEY (`id_compra`)
    REFERENCES `parque_atracciones`.`compra` (`id_compra`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```

## Población de la base de datos

La siguiente población contiene datos de prueba para las principales entidades de la base de datos. Los registros están relacionados entre sí mediante las claves foráneas, permitiendo probar clientes, temporadas, tipos de pase, compras, pagos, pases e historial de movimientos.

Los datos representan diferentes situaciones de compra, incluyendo pases anuales, pases de un día, alimentos, Pase Veloz y una actualización de nivel de pase.

```sql
USE parque_atracciones;

-- ============================================
-- POBLACIÓN DE LA BASE DE DATOS
-- ============================================

-- CLIENTE
INSERT INTO cliente
(numero_cliente, nombre, apellidos, fecha_nacimiento, correo, telefono, password, fecha_registro)
VALUES
('CLI0001', 'Carlos', 'Ramírez López', '2002-05-14', 'carlos.ramirez@gmail.com', '5512345678', '123456', '2026-01-10'),
('CLI0002', 'Mariana', 'Gómez Hernández', '2001-08-22', 'mariana.gomez@gmail.com', '5523456789', '123456', '2026-01-15'),
('CLI0003', 'Luis', 'Martínez Torres', '2003-03-10', 'luis.martinez@gmail.com', '5534567890', '123456', '2026-02-05'),
('CLI0004', 'Sofía', 'Hernández García', '2002-11-30', 'sofia.hernandez@gmail.com', '5545678901', '123456', '2026-02-20'),
('CLI0005', 'Diego', 'Sánchez Ruiz', '2000-07-18', 'diego.sanchez@gmail.com', '5556789012', '123456', '2026-03-01'),
('CLI0006', 'Fernanda', 'López Castillo', '2003-09-25', 'fernanda.lopez@gmail.com', '5567890123', '123456', '2026-03-12');


-- TEMPORADA
INSERT INTO temporada
(nombre, fecha_inicio, fecha_fin, estado)
VALUES
('Temporada 2026', '2026-01-01', '2026-12-31', 'Activa'),
('Temporada 2027', '2027-01-01', '2027-12-31', 'Programada');


-- TIPO_PASE
INSERT INTO tipo_pase
(nombre, modalidad, precio, nivel, categoria)
VALUES
('Casual', 'Anual', 1000.00, 1, 'Principal'),
('Dinámico', 'Anual', 1500.00, 2, 'Principal'),
('Fanático', 'Anual', 2000.00, 3, 'Principal'),
('Alimentos anual', 'Anual', 2000.00, NULL, 'Alimentos'),
('Pase Especial', 'Anual', 1500.00, NULL, 'Especial'),
('Pase de un día', 'Diario', 800.00, NULL, 'Principal'),
('Alimentos de un día', 'Diario', 500.00, NULL, 'Alimentos'),
('Pase Veloz', 'Diario', 2000.00, NULL, 'Veloz');


-- COMPRA
INSERT INTO compra
(id_cliente, fecha_compra, total, estado)
VALUES
(1, '2026-09-10 10:30:00', 1000.00, 'Completada'),
(2, '2026-09-12 11:00:00', 3500.00, 'Completada'),
(3, '2026-09-15 09:20:00', 1000.00, 'Completada'),
(4, '2026-09-20 12:15:00', 3300.00, 'Completada'),
(5, '2026-09-22 14:00:00', 3500.00, 'Completada'),
(6, '2026-09-25 16:30:00', 4800.00, 'Completada'),
(3, '2026-09-28 10:00:00', 1100.00, 'Completada'),
(4, '2026-09-30 13:00:00', 2000.00, 'Completada');


-- DETALLE_COMPRA
INSERT INTO detalle_compra
(id_compra, id_tipo_pase, cantidad, precio_unitario, subtotal)
VALUES
(1, 1, 1, 1000.00, 1000.00),

(2, 2, 1, 1500.00, 1500.00),
(2, 4, 1, 2000.00, 2000.00),

(3, 1, 1, 1000.00, 1000.00),

(4, 6, 1, 800.00, 800.00),
(4, 8, 1, 2000.00, 2000.00),
(4, 7, 1, 500.00, 500.00),

(5, 3, 1, 2000.00, 2000.00),
(5, 5, 1, 1500.00, 1500.00),

(6, 6, 1, 800.00, 800.00),
(6, 8, 2, 2000.00, 4000.00),

(7, 3, 1, 1100.00, 1100.00),

(8, 4, 1, 2000.00, 2000.00);


-- PAGO
INSERT INTO pago
(id_compra, metodo_pago, estado, monto, fecha_pago)
VALUES
(1, 'Tarjeta', 'Aprobado', 1000.00, '2026-09-10 10:31:00'),
(2, 'Tarjeta', 'Aprobado', 3500.00, '2026-09-12 11:01:00'),
(3, 'Efectivo', 'Aprobado', 1000.00, '2026-09-15 09:21:00'),
(4, 'Tarjeta', 'Aprobado', 3300.00, '2026-09-20 12:16:00'),
(5, 'Tarjeta', 'Aprobado', 3500.00, '2026-09-22 14:01:00'),
(6, 'Transferencia', 'Aprobado', 4800.00, '2026-09-25 16:31:00'),
(7, 'Tarjeta', 'Aprobado', 1100.00, '2026-09-28 10:01:00'),
(8, 'Tarjeta', 'Aprobado', 2000.00, '2026-09-30 13:01:00');


-- PASE
INSERT INTO pase
(numero_pase, id_cliente, id_detalle_compra, id_tipo_pase, id_temporada,
 fecha_compra, fecha_inicio, fecha_fin, fecha_uso, estado)
VALUES
('PAS000001', 1, 1, 1, 1,
 '2026-09-10', '2026-09-10', '2026-12-31', NULL, 'Activo'),

('PAS000002', 2, 2, 2, 1,
 '2026-09-12', '2026-09-12', '2026-12-31', NULL, 'Activo'),

('PAS000003', 2, 3, 4, 1,
 '2026-09-12', '2026-09-12', '2026-12-31', NULL, 'Activo'),

('PAS000004', 3, 4, 3, 1,
 '2026-09-15', '2026-09-15', '2026-12-31', NULL, 'Activo'),

('PAS000005', 4, 5, 6, NULL,
 '2026-09-20', '2026-09-20', '2026-09-20', '2026-09-20', 'Usado'),

('PAS000006', 4, 6, 8, NULL,
 '2026-09-20', '2026-09-20', '2026-09-20', '2026-09-20', 'Usado'),

('PAS000007', 4, 7, 7, NULL,
 '2026-09-20', '2026-09-20', '2026-09-20', '2026-09-20', 'Usado'),

('PAS000008', 5, 8, 3, 1,
 '2026-09-22', '2026-09-22', '2026-12-31', NULL, 'Activo'),

('PAS000009', 5, 9, 5, 1,
 '2026-09-22', '2026-09-22', '2026-12-31', NULL, 'Activo'),

('PAS000010', 6, 10, 6, NULL,
 '2026-09-25', '2026-09-25', '2026-09-25', '2026-09-25', 'Usado'),

('PAS000011', 6, 11, 8, NULL,
 '2026-09-25', '2026-09-25', '2026-09-25', '2026-09-25', 'Usado'),

('PAS000012', 6, 11, 8, NULL,
 '2026-09-25', '2026-09-25', '2026-09-25', '2026-09-25', 'Usado'),

('PAS000013', 4, 13, 4, 2,
 '2026-09-30', '2027-01-01', '2027-12-31', NULL, 'Activo');


-- HISTORIAL_PASE
INSERT INTO historial_pase
(id_pase, id_tipo_pase, id_compra, fecha, tipo_movimiento)
VALUES
(1, 1, 1, '2026-09-10 10:30:00', 'Alta'),

(2, 2, 2, '2026-09-12 11:00:00', 'Alta'),
(3, 4, 2, '2026-09-12 11:00:00', 'Alta'),

(4, 1, 3, '2026-09-15 09:20:00', 'Alta'),
(4, 3, 7, '2026-09-28 10:00:00', 'Upgrade'),

(5, 6, 4, '2026-09-20 12:15:00', 'Alta'),
(6, 8, 4, '2026-09-20 12:15:00', 'Alta'),
(7, 7, 4, '2026-09-20 12:15:00', 'Alta'),

(8, 3, 5, '2026-09-22 14:00:00', 'Alta'),
(9, 5, 5, '2026-09-22 14:00:00', 'Alta'),

(10, 6, 6, '2026-09-25 16:30:00', 'Alta'),
(11, 8, 6, '2026-09-25 16:30:00', 'Alta'),
(12, 8, 6, '2026-09-25 16:30:00', 'Alta'),

(13, 4, 8, '2026-09-30 13:00:00', 'Alta');
```