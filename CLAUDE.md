# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# Pymdu_Lyon — Tese de doutorado (acoplamento UCM–BEM)

## Contexto
Repositório do pipeline de microclima urbano (pymdu / UMEP) para a tese sobre
acoplamento bidirecional iterativo UCM–BEM e conforto térmico interior/exterior.
- Área de estudo: microescala.
- Bounding box (lon/lat): `[4.803815, 45.763751, 4.807109, 45.766041]`.
- Sistema de coordenadas de referência: **EPSG:2154 (Lambert-93)**.
- Repositório GitHub: https://github.com/lkaucz/Pymdu_Lyon (conta `lkaucz`).

## Ambiente — REGRAS CRÍTICAS (não quebrar)
- Ambiente conda `pymdu` em
  `C:\Users\leonardo.kaucz\AppData\Local\miniforge3\envs\pymdu` (Python 3.11).
  Ativar **antes** de qualquer coisa.
- **IMPORTANT: numpy DEVE permanecer em 1.26.4.** O UMEP 2025a é incompatível
  com numpy 2.x. Se o numpy mudar, restaurar com:
  `conda install -c conda-forge -y "numpy=1.26.4"`
- **YOU MUST**, após CADA `poetry install`, refazer a correção do conflito do PROJ
  (rasterio vindo do pip vs pyproj vindo do conda-forge):
  `pip uninstall -y rasterio pyproj shapely`
  `conda install -c conda-forge -y rasterio pyproj shapely`
- `JAVA_HOME` deve ser
  `C:\Users\leonardo.kaucz\AppData\Local\miniforge3\envs\pymdu\Library\lib\jvm`
- Plugin UMEP do QGIS em
  `C:\Users\leonardo.kaucz\AppData\Roaming\QGIS\QGIS3\profiles\default\python\plugins\processing_umep`
- No notebook, definir
  `env_dir = Path.home() / 'AppData' / 'Local' / 'miniforge3' / 'envs' / 'pymdu'`

## Patches já aplicados ao código (NÃO reverter)
- `pymdu/image/Lidar.py`: endpoint WFS LiDAR HD do IGN atualizado para
  `/wfs/ows`, camada `IGNF_NUAGES-DE-POINTS-LIDAR-HD:dalle`, e o sufixo
  `,EPSG:2154` deve ser anexado à string do bbox. O endpoint antigo
  (`/private/wfs` + `apikey=interface_catalogue` + camada
  `IGNF_LIDAR-HD_TA:nuage-dalle`) está descontinuado.
- Conversor meteorológico EPW→UMEP (via pvlib): converte o EPW Lyon-Bron
  TMYx 2004–2018 para o formato de 24 colunas separado por espaços exigido pelo
  loader do SOLWEIG.
- UTCI: anexar `.utci` ao objeto retornado por
  `pythermalcomfort.models.utci()`.

## Dados
- COSIA (cobertura do solo, IGN): departamento 69, ano 2023, Lambert-93.
- BD TOPO (IGN): alturas e pegadas dos edifícios.

## Pipeline (ordem do notebook Demo_PyMDU_Atelier)
1. Extração de edifícios
2. Cobertura do solo COSIA
3. DEM
4. Extração de árvores do LiDAR
5. Geração do DSM
6. CDSM / TDSM
7. Wall Height / Aspect
8. Sky View Factor
9. SOLWEIG (Tmrt)
10. URock (campo de vento)
11. UTCI

Dia de verão representativo: dia 181 (30 de junho), pico ~33 °C,
Kdn de pico ~932 W/m².

## Convenções de trabalho
- Responder em **português brasileiro**.
- Ser **conciso e direto**; fornecer comandos prontos para colar; evitar
  explicação excessiva.
- **Diagnosticar antes de agir** (verificar `git status`, estado do ambiente).
  NÃO sugerir reinstalações sem evidência do problema.
- **Não inventar**: deixar em branco / sinalizar incerteza em vez de fabricar
  valores ou capacidades.

## Git
- NÃO versionar `results_demo/` nem `landcover.tif` (manter no `.gitignore`).
