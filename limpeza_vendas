# ============================================================
# PARTE 1 — Carregar pacotes e ler os dados
# ============================================================
# SEMPRE carregar os pacotes primeiro. O pipe %>% vem do dplyr.
# (instalar só é necessário uma vez; se já instalou, pode comentar)

# install.packages(c("readxl", "dplyr", "janitor", "lubridate", "stringr"))

library(readxl)
library(dplyr)
library(janitor)
library(lubridate)
library(stringr)

getwd()           
list.files()       

vendas_sujas <- read_excel("dados_vendas_sujos.xlsx")

head(vendas_sujas)
str(vendas_sujas)
names(vendas_sujas)   # conferir nomes originais das colunas


# ============================================================
# PARTE 2 — Padronizar nomes de coluna e criar base de trabalho
# ============================================================
# clean_names() deixa tudo em snake_case minúsculo:
# "Data_Venda" -> "data_venda", "Valor Unitário" -> "valor_unitario", etc.

vendas_limpas <- vendas_sujas %>% clean_names()
names(vendas_limpas)   # confirme: data_venda? valor_unitario? quantidade?


# ============================================================
# PARTE 3 — Diagnóstico: quais valores de data NÃO parseiam?
# ============================================================
# Roda na base ORIGINAL (vendas_sujas) para ver o que está sujo.
# Nomes já podem estar minúsculos por causa do clean_names? Não —
# aqui usamos vendas_sujas (nomes originais). Se a coluna for Data_Venda:

vendas_sujas %>%
  mutate(Data_Venda = as.character(Data_Venda)) %>%
  filter(!is.na(Data_Venda),
         is.na(parse_date_time(Data_Venda, orders = c("dmy","ymd","mdy")))) %>%
  pull(Data_Venda) %>%
  unique() %>%
  head(30)

# Se a coluna na base original estiver em minúsculo (data_venda), troque
# "Data_Venda" por "data_venda" no bloco acima.


# ============================================================
# PARTE 4 — Limpar e converter a coluna de datas
# ============================================================
vendas_limpas <- vendas_limpas %>%
  mutate(
    data_venda = str_trim(as.character(data_venda)),
    # trata "nd", "ND", vazio e NA de verdade como NA
    data_venda = if_else(
      is.na(data_venda) | str_to_lower(data_venda) %in% c("nd", ""),
      NA_character_,
      data_venda
    ),
    data_venda = parse_date_time(data_venda, orders = c("dmy", "ymd", "mdy"))
  )

# Conferir quantas datas válidas vs NA
sum(!is.na(vendas_limpas$data_venda))   # válidas
sum(is.na(vendas_limpas$data_venda))    # NA (inclui os "nd" e vazios)


# ============================================================
# PARTE 5 — Limpar e converter a coluna de quantidade
# ============================================================
# Remove tudo que não for dígito, ponto ou sinal de menos.
vendas_limpas <- vendas_limpas %>%
  mutate(
    quantidade = str_remove_all(as.character(quantidade), "[^0-9.-]"),
    quantidade = as.numeric(quantidade)
  )

summary(vendas_limpas$quantidade)


# ============================================================
# PARTE 6 — Limpar e converter a coluna de valor unitário
# ============================================================
# Remove "R", "$", "%" e espaços; remove pontos de milhar;
# troca vírgula decimal por ponto; trata "-" e vazio como NA.
vendas_limpas <- vendas_limpas %>%
  mutate(
    valor_unitario = as.character(valor_unitario),
    valor_unitario = str_remove_all(valor_unitario, "[R$%[:space:]]"),
    valor_unitario = str_remove_all(valor_unitario, "\\.(?=\\d{3}(\\D|$))"),
    valor_unitario = str_replace_all(valor_unitario, ",", "."),
    valor_unitario = if_else(
      is.na(valor_unitario) | valor_unitario %in% c("-", ""),
      NA_character_,
      valor_unitario
    ),
    valor_unitario = as.numeric(valor_unitario)
  )

summary(vendas_limpas$valor_unitario)


# ============================================================
# PARTE 7 — Diagnóstico final
# ============================================================
str(vendas_limpas)
head(vendas_limpas)

# Quantos NA restaram em cada coluna
colSums(is.na(vendas_limpas))

# Amostras que ainda falharam (se houver)
vendas_limpas %>%
  filter(is.na(data_venda)) %>%
  distinct(data_venda) %>%
  head(30)

vendas_limpas %>%
  filter(is.na(valor_unitario)) %>%
  distinct(valor_unitario) %>%
  head(30)
