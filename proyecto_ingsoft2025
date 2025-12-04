--
-- PostgreSQL database dump
--

\restrict QarPSsmVVenl1OY0iInlKlrwXo5b2FZkLZnsQyMqyh1J5ds809lRZlbYexQQ4Dp

-- Dumped from database version 15.15
-- Dumped by pg_dump version 15.15

-- Started on 2025-12-03 19:11:00

SET statement_timeout = 0;
SET lock_timeout = 0;
SET idle_in_transaction_session_timeout = 0;
SET client_encoding = 'UTF8';
SET standard_conforming_strings = on;
SELECT pg_catalog.set_config('search_path', '', false);
SET check_function_bodies = false;
SET xmloption = content;
SET client_min_messages = warning;
SET row_security = off;

--
-- TOC entry 2 (class 3079 OID 16772)
-- Name: pgcrypto; Type: EXTENSION; Schema: -; Owner: -
--

CREATE EXTENSION IF NOT EXISTS pgcrypto WITH SCHEMA public;


--
-- TOC entry 3417 (class 0 OID 0)
-- Dependencies: 2
-- Name: EXTENSION pgcrypto; Type: COMMENT; Schema: -; Owner: 
--

COMMENT ON EXTENSION pgcrypto IS 'cryptographic functions';


--
-- TOC entry 268 (class 1255 OID 16896)
-- Name: sp_registrar_usuario_completo(character varying, character varying, character varying, integer, integer, integer, character varying, character varying, character varying, character varying, character varying, integer, integer, character varying, character varying, character varying, integer, integer, character varying); Type: FUNCTION; Schema: public; Owner: postgres
--

CREATE FUNCTION public.sp_registrar_usuario_completo(p_email character varying, p_password character varying, p_nombre character varying, p_tipo_usuario integer, p_genero integer, p_pronombre integer, p_alias_direccion character varying, p_calle character varying, p_no_exterior character varying, p_no_interior character varying, p_codigo_postal character varying, p_estado integer, p_municipio integer, p_localidad character varying, p_colonia character varying, p_referencias character varying, p_tipo_domicilio integer, p_tipo_red integer, p_enlace_red character varying) RETURNS uuid
    LANGUAGE plpgsql
    AS $$
DECLARE
    v_id_usuario UUID;
    v_nombre_fragmentado JSONB;
BEGIN
    -- Validar que el email no exista
    IF EXISTS (SELECT 1 FROM usuarios WHERE email = p_email) THEN
        RAISE EXCEPTION 'El email % ya está registrado', p_email;
    END IF;
    
    -- Crear nombre fragmentado como JSON
    v_nombre_fragmentado := jsonb_build_object(
        'nombre_completo', p_nombre,
        'fragmentos', string_to_array(p_nombre, ' ')
    );
    
    -- Insertar en usuarios
    INSERT INTO usuarios (
        email,
        password_hash,
        nombre,
        nombre_fragmentado,
        id_tipo_usuario,
        id_estatus
    ) VALUES (
        p_email,
        crypt(p_password, gen_salt('bf')),
        p_nombre,
        v_nombre_fragmentado,
        p_tipo_usuario::SMALLINT,
        1
    ) RETURNING id_usuario INTO v_id_usuario;
    
    -- Insertar en perfiles_usuarios
    INSERT INTO perfiles_usuarios (
        id_usuario,
        id_genero,
        id_pronombre
    ) VALUES (
        v_id_usuario,
        p_genero::SMALLINT,
        p_pronombre::SMALLINT
    );
    
    -- Insertar en seguridad_usuario
    INSERT INTO seguridad_usuario (
        id_usuario,
        email_verificado,
        intentos_fallidos
    ) VALUES (
        v_id_usuario,
        FALSE,
        0
    );
    
    -- Insertar en preferencias
    INSERT INTO preferencias (
        id_usuario,
        notificaciones_email,
        notificaciones_push,
        idioma,
        zona_horaria
    ) VALUES (
        v_id_usuario,
        TRUE,
        TRUE,
        'es',
        'America/Mexico_City'
    );
    
    -- Insertar en direcciones
    INSERT INTO direcciones (
        id_usuario,
        alias,
        calle,
        no_exterior,
        no_interior,
        codigo_postal,
        id_estado,
        id_municipio,
        localidad,
        colonia,
        referencias,
        id_tipo_domicilio,
        es_principal
    ) VALUES (
        v_id_usuario,
        p_alias_direccion,
        p_calle,
        p_no_exterior,
        p_no_interior,
        p_codigo_postal,
        p_estado::SMALLINT,
        p_municipio::SMALLINT,
        p_localidad,
        p_colonia,
        p_referencias,
        p_tipo_domicilio::SMALLINT,
        TRUE
    );
    
    -- Insertar en redes_sociales
    INSERT INTO redes_sociales (
        id_usuario,
        id_tipo_red,
        enlace
    ) VALUES (
        v_id_usuario,
        p_tipo_red::SMALLINT,
        p_enlace_red
    );
    
    RETURN v_id_usuario;
    
EXCEPTION
    WHEN OTHERS THEN
        RAISE EXCEPTION 'Error al registrar usuario: %', SQLERRM;
END;
$$;


ALTER FUNCTION public.sp_registrar_usuario_completo(p_email character varying, p_password character varying, p_nombre character varying, p_tipo_usuario integer, p_genero integer, p_pronombre integer, p_alias_direccion character varying, p_calle character varying, p_no_exterior character varying, p_no_interior character varying, p_codigo_postal character varying, p_estado integer, p_municipio integer, p_localidad character varying, p_colonia character varying, p_referencias character varying, p_tipo_domicilio integer, p_tipo_red integer, p_enlace_red character varying) OWNER TO postgres;

SET default_tablespace = '';

SET default_table_access_method = heap;

--
-- TOC entry 219 (class 1259 OID 16864)
-- Name: direcciones; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.direcciones (
    id_direccion uuid DEFAULT gen_random_uuid() NOT NULL,
    id_usuario uuid,
    alias character varying,
    calle character varying,
    no_exterior character varying,
    no_interior character varying,
    codigo_postal character varying,
    id_estado smallint,
    id_municipio smallint,
    localidad character varying,
    colonia character varying,
    referencias character varying,
    id_tipo_domicilio smallint,
    es_principal boolean DEFAULT true
);


ALTER TABLE public.direcciones OWNER TO postgres;

--
-- TOC entry 216 (class 1259 OID 16820)
-- Name: perfiles_usuarios; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.perfiles_usuarios (
    id_perfil uuid DEFAULT gen_random_uuid() NOT NULL,
    id_usuario uuid NOT NULL,
    id_genero smallint,
    id_pronombre smallint,
    objetivo_inicial character varying(200),
    intereses jsonb DEFAULT '[]'::jsonb
);


ALTER TABLE public.perfiles_usuarios OWNER TO postgres;

--
-- TOC entry 218 (class 1259 OID 16847)
-- Name: preferencias; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.preferencias (
    id_preferencias uuid DEFAULT gen_random_uuid() NOT NULL,
    id_usuario uuid NOT NULL,
    notificaciones_email boolean DEFAULT true,
    notificaciones_push boolean DEFAULT true,
    idioma character varying DEFAULT 'es'::character varying,
    zona_horaria character varying DEFAULT 'America/Mexico_City'::character varying
);


ALTER TABLE public.preferencias OWNER TO postgres;

--
-- TOC entry 220 (class 1259 OID 16878)
-- Name: redes_sociales; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.redes_sociales (
    id_red uuid DEFAULT gen_random_uuid() NOT NULL,
    id_usuario uuid,
    id_tipo_red smallint,
    enlace character varying
);


ALTER TABLE public.redes_sociales OWNER TO postgres;

--
-- TOC entry 217 (class 1259 OID 16834)
-- Name: seguridad_usuario; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.seguridad_usuario (
    id_seguridad uuid DEFAULT gen_random_uuid() NOT NULL,
    id_usuario uuid NOT NULL,
    email_verificado boolean DEFAULT false,
    intentos_fallidos smallint DEFAULT 0
);


ALTER TABLE public.seguridad_usuario OWNER TO postgres;

--
-- TOC entry 215 (class 1259 OID 16809)
-- Name: usuarios; Type: TABLE; Schema: public; Owner: postgres
--

CREATE TABLE public.usuarios (
    id_usuario uuid DEFAULT gen_random_uuid() NOT NULL,
    id_tipo_usuario smallint NOT NULL,
    email character varying NOT NULL,
    password_hash character varying NOT NULL,
    nombre character varying NOT NULL,
    nombre_fragmentado jsonb,
    id_estatus smallint DEFAULT 1 NOT NULL
);


ALTER TABLE public.usuarios OWNER TO postgres;

--
-- TOC entry 3410 (class 0 OID 16864)
-- Dependencies: 219
-- Data for Name: direcciones; Type: TABLE DATA; Schema: public; Owner: postgres
--

COPY public.direcciones (id_direccion, id_usuario, alias, calle, no_exterior, no_interior, codigo_postal, id_estado, id_municipio, localidad, colonia, referencias, id_tipo_domicilio, es_principal) FROM stdin;
825bdff6-96a4-4209-91a4-c851ea3e7ee5	e33c9b47-45fb-4372-98bd-6dee773f7aa4	Casa	Av. Sol	123	\N	54030	1	1	Centro	Colonia Sol	Frente al parque	1	t
3106876d-2c74-4ada-908b-83e4713c90cb	3f6b96b8-6156-4004-9292-015aeead5eba	Casa	Calle Test	123	\N	00000	1	1	Test	Test	Test	1	t
9583d008-d2ad-481c-972a-a71a978a8cb2	1e79f790-c4d1-4358-b1cd-7fade387621b	Oficina	Av. Reforma	456	\N	11560	1	1	Polanco	Polanco	Cerca del Auditorio	2	t
\.


--
-- TOC entry 3407 (class 0 OID 16820)
-- Dependencies: 216
-- Data for Name: perfiles_usuarios; Type: TABLE DATA; Schema: public; Owner: postgres
--

COPY public.perfiles_usuarios (id_perfil, id_usuario, id_genero, id_pronombre, objetivo_inicial, intereses) FROM stdin;
02919ca0-f593-4f79-b06f-bddf3315b7da	e33c9b47-45fb-4372-98bd-6dee773f7aa4	2	2	\N	[]
831052be-daf6-41b1-9e29-9be655e9933c	3f6b96b8-6156-4004-9292-015aeead5eba	1	1	\N	[]
68334ff4-5adc-4c8e-964e-5d99461fd16f	1e79f790-c4d1-4358-b1cd-7fade387621b	1	1	\N	[]
\.


--
-- TOC entry 3409 (class 0 OID 16847)
-- Dependencies: 218
-- Data for Name: preferencias; Type: TABLE DATA; Schema: public; Owner: postgres
--

COPY public.preferencias (id_preferencias, id_usuario, notificaciones_email, notificaciones_push, idioma, zona_horaria) FROM stdin;
9d88197a-75db-4f69-aa3c-e0c5c6587c5f	e33c9b47-45fb-4372-98bd-6dee773f7aa4	t	t	es	America/Mexico_City
87a7444b-3457-4327-958d-1b1719fd93d3	3f6b96b8-6156-4004-9292-015aeead5eba	t	t	es	America/Mexico_City
cbeb6bf7-ba17-4435-8469-5499815fce81	1e79f790-c4d1-4358-b1cd-7fade387621b	t	t	es	America/Mexico_City
\.


--
-- TOC entry 3411 (class 0 OID 16878)
-- Dependencies: 220
-- Data for Name: redes_sociales; Type: TABLE DATA; Schema: public; Owner: postgres
--

COPY public.redes_sociales (id_red, id_usuario, id_tipo_red, enlace) FROM stdin;
00cf0644-17ce-4f39-89cd-ab1c32c61141	e33c9b47-45fb-4372-98bd-6dee773f7aa4	1	facebook.com/luis
7c3e09e6-206c-4355-bb69-2c01791219ae	3f6b96b8-6156-4004-9292-015aeead5eba	1	test.com
f69b1216-8435-4f0f-b0aa-4a71d2bd43d2	1e79f790-c4d1-4358-b1cd-7fade387621b	2	twitter.com/maria
\.


--
-- TOC entry 3408 (class 0 OID 16834)
-- Dependencies: 217
-- Data for Name: seguridad_usuario; Type: TABLE DATA; Schema: public; Owner: postgres
--

COPY public.seguridad_usuario (id_seguridad, id_usuario, email_verificado, intentos_fallidos) FROM stdin;
2b56a74e-591b-4bb2-b8e6-48296c5636e7	e33c9b47-45fb-4372-98bd-6dee773f7aa4	f	0
70b4ed97-1614-4650-a8e9-a99623437cda	3f6b96b8-6156-4004-9292-015aeead5eba	f	0
74e229f4-718a-45de-98d7-4a0a59c112c7	1e79f790-c4d1-4358-b1cd-7fade387621b	f	0
\.


--
-- TOC entry 3406 (class 0 OID 16809)
-- Dependencies: 215
-- Data for Name: usuarios; Type: TABLE DATA; Schema: public; Owner: postgres
--

COPY public.usuarios (id_usuario, id_tipo_usuario, email, password_hash, nombre, nombre_fragmentado, id_estatus) FROM stdin;
e33c9b47-45fb-4372-98bd-6dee773f7aa4	1	luis@gmail.com	$2a$06$IKGpTBQ9VI4e92XgtUv.9.m0uZteCYahE/AcdWR.u5pCq4yiRI1aS	Luis Martínez	{"fragmentos": ["Luis", "Martínez"], "nombre_completo": "Luis Martínez"}	1
3f6b96b8-6156-4004-9292-015aeead5eba	1	test@gmail.com	$2a$06$RmdAyFDdlOplsd7mDhHL5Ody9DmJ6JvHwomEhkkjQZNzEZGyKT7.i	Test User	{"fragmentos": ["Test", "User"], "nombre_completo": "Test User"}	1
1e79f790-c4d1-4358-b1cd-7fade387621b	1	maria@gmail.com	$2a$06$QBqveS55kBXXeTWPcy4NX.eYKK84AGri9clJukGpB4XwTvOS3u42W	María González	{"fragmentos": ["María", "González"], "nombre_completo": "María González"}	1
\.


--
-- TOC entry 3256 (class 2606 OID 16872)
-- Name: direcciones direcciones_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.direcciones
    ADD CONSTRAINT direcciones_pkey PRIMARY KEY (id_direccion);


--
-- TOC entry 3250 (class 2606 OID 16828)
-- Name: perfiles_usuarios perfiles_usuarios_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.perfiles_usuarios
    ADD CONSTRAINT perfiles_usuarios_pkey PRIMARY KEY (id_perfil);


--
-- TOC entry 3254 (class 2606 OID 16858)
-- Name: preferencias preferencias_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.preferencias
    ADD CONSTRAINT preferencias_pkey PRIMARY KEY (id_preferencias);


--
-- TOC entry 3258 (class 2606 OID 16885)
-- Name: redes_sociales redes_sociales_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.redes_sociales
    ADD CONSTRAINT redes_sociales_pkey PRIMARY KEY (id_red);


--
-- TOC entry 3252 (class 2606 OID 16841)
-- Name: seguridad_usuario seguridad_usuario_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.seguridad_usuario
    ADD CONSTRAINT seguridad_usuario_pkey PRIMARY KEY (id_seguridad);


--
-- TOC entry 3246 (class 2606 OID 16819)
-- Name: usuarios usuarios_email_key; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.usuarios
    ADD CONSTRAINT usuarios_email_key UNIQUE (email);


--
-- TOC entry 3248 (class 2606 OID 16817)
-- Name: usuarios usuarios_pkey; Type: CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.usuarios
    ADD CONSTRAINT usuarios_pkey PRIMARY KEY (id_usuario);


--
-- TOC entry 3262 (class 2606 OID 16873)
-- Name: direcciones direcciones_id_usuario_fkey; Type: FK CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.direcciones
    ADD CONSTRAINT direcciones_id_usuario_fkey FOREIGN KEY (id_usuario) REFERENCES public.usuarios(id_usuario) ON DELETE CASCADE;


--
-- TOC entry 3259 (class 2606 OID 16829)
-- Name: perfiles_usuarios perfiles_usuarios_id_usuario_fkey; Type: FK CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.perfiles_usuarios
    ADD CONSTRAINT perfiles_usuarios_id_usuario_fkey FOREIGN KEY (id_usuario) REFERENCES public.usuarios(id_usuario) ON DELETE CASCADE;


--
-- TOC entry 3261 (class 2606 OID 16859)
-- Name: preferencias preferencias_id_usuario_fkey; Type: FK CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.preferencias
    ADD CONSTRAINT preferencias_id_usuario_fkey FOREIGN KEY (id_usuario) REFERENCES public.usuarios(id_usuario) ON DELETE CASCADE;


--
-- TOC entry 3263 (class 2606 OID 16886)
-- Name: redes_sociales redes_sociales_id_usuario_fkey; Type: FK CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.redes_sociales
    ADD CONSTRAINT redes_sociales_id_usuario_fkey FOREIGN KEY (id_usuario) REFERENCES public.usuarios(id_usuario) ON DELETE CASCADE;


--
-- TOC entry 3260 (class 2606 OID 16842)
-- Name: seguridad_usuario seguridad_usuario_id_usuario_fkey; Type: FK CONSTRAINT; Schema: public; Owner: postgres
--

ALTER TABLE ONLY public.seguridad_usuario
    ADD CONSTRAINT seguridad_usuario_id_usuario_fkey FOREIGN KEY (id_usuario) REFERENCES public.usuarios(id_usuario) ON DELETE CASCADE;


-- Completed on 2025-12-03 19:11:00

--
-- PostgreSQL database dump complete
--

\unrestrict QarPSsmVVenl1OY0iInlKlrwXo5b2FZkLZnsQyMqyh1J5ds809lRZlbYexQQ4Dp

