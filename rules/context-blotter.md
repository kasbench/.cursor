# Context for the Blotter Microservice

## PostgreSQL Schema

```sql
-- ** Database generated with pgModeler (PostgreSQL Database Modeler).
-- ** pgModeler version: 1.2.0-beta1
-- ** PostgreSQL version: 17.0
-- ** Project Site: pgmodeler.io
-- ** Model Author: ---

-- ** Database creation must be performed outside a multi lined SQL file. 
-- ** These commands were put in this file only as a convenience.

-- object: postgres | type: DATABASE --
-- DROP DATABASE IF EXISTS postgres;
CREATE DATABASE postgres;
-- ddl-end --


SET search_path TO pg_catalog,public;
-- ddl-end --

-- object: public.blotter | type: TABLE --
-- DROP TABLE IF EXISTS public.blotter CASCADE;
CREATE TABLE public.blotter (
	id serial NOT NULL,
	name varchar(60) NOT NULL,
	auto_populate bit NOT NULL DEFAULT 0::bit,
	security_type_id integer,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT blotter_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.blotter OWNER TO postgres;
-- ddl-end --

-- object: public.security | type: TABLE --
-- DROP TABLE IF EXISTS public.security CASCADE;
CREATE TABLE public.security (
	id serial NOT NULL,
	ticker varchar(50) NOT NULL,
	description varchar(200),
	security_type_id integer NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT security_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.security OWNER TO postgres;
-- ddl-end --

-- object: public.security_type | type: TABLE --
-- DROP TABLE IF EXISTS public.security_type CASCADE;
CREATE TABLE public.security_type (
	id integer NOT NULL,
	abbreviation varchar(10) NOT NULL,
	description varchar(100) NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT security_type_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.security_type OWNER TO postgres;
-- ddl-end --

-- object: security_type_security_fk | type: CONSTRAINT --
-- ALTER TABLE public.security DROP CONSTRAINT IF EXISTS security_type_security_fk CASCADE;
ALTER TABLE public.security ADD CONSTRAINT security_type_security_fk FOREIGN KEY (security_type_id)
REFERENCES public.security_type (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: public."order" | type: TABLE --
-- DROP TABLE IF EXISTS public."order" CASCADE;
CREATE TABLE public."order" (
	id serial NOT NULL,
	blotter_id integer,
	security_id integer NOT NULL,
	quantity decimal(18,8),
	order_timestamp timestamptz,
	version integer NOT NULL DEFAULT 0,
	order_type_id integer NOT NULL,
	order_status_id integer,
	CONSTRAINT order_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public."order" OWNER TO postgres;
-- ddl-end --

-- object: security_type_blotter_fk | type: CONSTRAINT --
-- ALTER TABLE public.blotter DROP CONSTRAINT IF EXISTS security_type_blotter_fk CASCADE;
ALTER TABLE public.blotter ADD CONSTRAINT security_type_blotter_fk FOREIGN KEY (security_type_id)
REFERENCES public.security_type (id) MATCH FULL
ON DELETE SET NULL ON UPDATE CASCADE;
-- ddl-end --

-- object: blotter_order_fk | type: CONSTRAINT --
-- ALTER TABLE public."order" DROP CONSTRAINT IF EXISTS blotter_order_fk CASCADE;
ALTER TABLE public."order" ADD CONSTRAINT blotter_order_fk FOREIGN KEY (blotter_id)
REFERENCES public.blotter (id) MATCH FULL
ON DELETE SET NULL ON UPDATE CASCADE;
-- ddl-end --

-- object: security_fk | type: CONSTRAINT --
-- ALTER TABLE public."order" DROP CONSTRAINT IF EXISTS security_fk CASCADE;
ALTER TABLE public."order" ADD CONSTRAINT security_fk FOREIGN KEY (security_id)
REFERENCES public.security (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: public.order_type | type: TABLE --
-- DROP TABLE IF EXISTS public.order_type CASCADE;
CREATE TABLE public.order_type (
	id integer NOT NULL,
	abbreviation varchar(10) NOT NULL,
	description varchar(60) NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT order_type_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.order_type OWNER TO postgres;
-- ddl-end --

-- object: order_type_order_fk | type: CONSTRAINT --
-- ALTER TABLE public."order" DROP CONSTRAINT IF EXISTS order_type_order_fk CASCADE;
ALTER TABLE public."order" ADD CONSTRAINT order_type_order_fk FOREIGN KEY (order_type_id)
REFERENCES public.order_type (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: public.trade | type: TABLE --
-- DROP TABLE IF EXISTS public.trade CASCADE;
CREATE TABLE public.trade (
	id serial NOT NULL,
	destination_id integer NOT NULL,
	block_id integer NOT NULL,
	quantity decimal(18,8),
	trade_type_id integer NOT NULL,
	filled_quantity decimal(18,8) NOT NULL DEFAULT 0,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT trade_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.trade OWNER TO postgres;
-- ddl-end --

-- object: public.destination | type: TABLE --
-- DROP TABLE IF EXISTS public.destination CASCADE;
CREATE TABLE public.destination (
	id integer NOT NULL,
	abbreviation varchar(20) NOT NULL,
	description varchar(100) NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT destination_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.destination OWNER TO postgres;
-- ddl-end --

-- object: destination_trade_fk | type: CONSTRAINT --
-- ALTER TABLE public.trade DROP CONSTRAINT IF EXISTS destination_trade_fk CASCADE;
ALTER TABLE public.trade ADD CONSTRAINT destination_trade_fk FOREIGN KEY (destination_id)
REFERENCES public.destination (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: public.block | type: TABLE --
-- DROP TABLE IF EXISTS public.block CASCADE;
CREATE TABLE public.block (
	id serial NOT NULL,
	security_id integer NOT NULL,
	order_type_id integer NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT block_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.block OWNER TO postgres;
-- ddl-end --

-- object: public.order_status | type: TABLE --
-- DROP TABLE IF EXISTS public.order_status CASCADE;
CREATE TABLE public.order_status (
	id integer NOT NULL,
	abbreviation varchar(20) NOT NULL,
	description varchar(60) NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT order_status_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.order_status OWNER TO postgres;
-- ddl-end --

-- object: order_status_order_fk | type: CONSTRAINT --
-- ALTER TABLE public."order" DROP CONSTRAINT IF EXISTS order_status_order_fk CASCADE;
ALTER TABLE public."order" ADD CONSTRAINT order_status_order_fk FOREIGN KEY (order_status_id)
REFERENCES public.order_status (id) MATCH FULL
ON DELETE SET NULL ON UPDATE CASCADE;
-- ddl-end --

-- object: public.block_allocation | type: TABLE --
-- DROP TABLE IF EXISTS public.block_allocation CASCADE;
CREATE TABLE public.block_allocation (
	id serial NOT NULL,
	order_id integer NOT NULL,
	block_id integer NOT NULL,
	quantity decimal(18,8) NOT NULL,
	filled_quantity decimal(18,8) NOT NULL DEFAULT 0,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT block_allocation_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.block_allocation OWNER TO postgres;
-- ddl-end --

-- object: order_block_allocation_fk | type: CONSTRAINT --
-- ALTER TABLE public.block_allocation DROP CONSTRAINT IF EXISTS order_block_allocation_fk CASCADE;
ALTER TABLE public.block_allocation ADD CONSTRAINT order_block_allocation_fk FOREIGN KEY (order_id)
REFERENCES public."order" (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: block_block_allocation_fk | type: CONSTRAINT --
-- ALTER TABLE public.block_allocation DROP CONSTRAINT IF EXISTS block_block_allocation_fk CASCADE;
ALTER TABLE public.block_allocation ADD CONSTRAINT block_block_allocation_fk FOREIGN KEY (block_id)
REFERENCES public.block (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: security_block_fk | type: CONSTRAINT --
-- ALTER TABLE public.block DROP CONSTRAINT IF EXISTS security_block_fk CASCADE;
ALTER TABLE public.block ADD CONSTRAINT security_block_fk FOREIGN KEY (security_id)
REFERENCES public.security (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: order_type_block_fk | type: CONSTRAINT --
-- ALTER TABLE public.block DROP CONSTRAINT IF EXISTS order_type_block_fk CASCADE;
ALTER TABLE public.block ADD CONSTRAINT order_type_block_fk FOREIGN KEY (order_type_id)
REFERENCES public.order_type (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: block_trade_fk | type: CONSTRAINT --
-- ALTER TABLE public.trade DROP CONSTRAINT IF EXISTS block_trade_fk CASCADE;
ALTER TABLE public.trade ADD CONSTRAINT block_trade_fk FOREIGN KEY (block_id)
REFERENCES public.block (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --

-- object: public.trade_type | type: TABLE --
-- DROP TABLE IF EXISTS public.trade_type CASCADE;
CREATE TABLE public.trade_type (
	id integer NOT NULL,
	abbreviation varchar(10) NOT NULL,
	description varchar(60) NOT NULL,
	version integer NOT NULL DEFAULT 0,
	CONSTRAINT trade_type_pk PRIMARY KEY (id)
);
-- ddl-end --
ALTER TABLE public.trade_type OWNER TO postgres;
-- ddl-end --

-- object: trade_type_trade_fk | type: CONSTRAINT --
-- ALTER TABLE public.trade DROP CONSTRAINT IF EXISTS trade_type_trade_fk CASCADE;
ALTER TABLE public.trade ADD CONSTRAINT trade_type_trade_fk FOREIGN KEY (trade_type_id)
REFERENCES public.trade_type (id) MATCH FULL
ON DELETE RESTRICT ON UPDATE CASCADE;
-- ddl-end --




```