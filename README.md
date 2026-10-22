# limpeza_vendas-em-R
# Limpeza de dados de vendas em R

Script em R para limpeza e padronização de uma base de vendas "suja":
datas em formatos mistos (dmy/ymd/mdy e valores como `"nd"`), quantidades
com texto e valores monetários no formato brasileiro (`R$ 1.234,56`).

## Requisitos

- R >= 4.3
- Pacotes: `readxl`, `dplyr`, `janitor`, `lubridate`, `stringr`

Instalação dos pacotes:

```r
install.packages(c("readxl", "dplyr", "janitor", "lubridate", "stringr"))
