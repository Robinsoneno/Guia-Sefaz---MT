# Guia Sefaz - MT

 # Automação de Preenchimento de Guia ICMS

Este projeto é uma automação de preenchimento de formulários para a geração de guias ICMS (Imposto sobre Circulação de Mercadorias e Serviços) no site da Secretaria da Fazenda do Estado de Mato Grosso (SEFAZ-MT). O script utiliza a biblioteca Playwright para interagir com a página web e preencher os campos necessários com base em dados extraídos de um arquivo de texto.

Hoje
Crie um arquivo readme para github do código abaixo: from datetime import datetime, timedelta from playwright.sync_api import Playwright, sync_playwright, TimeoutError import os import keyboard import time import logging from pywinauto import Application # Comando para definir a impressora padrão ativa_pdf = 'wmic printer where name="Microsoft Print to PDF" call setdefaultprinter' # Comando para definir a impressora padrão # Comando para definir a impressora padrão ativa_padrao = 'wmic printer where name="HP_MFP_M225-M226_PCL_6" call setdefaultprinter' # Ativar impressora pdf os.system(ativa_pdf) # Configura o logging para salvar em um arquivo específico path_logs = r"c:\base\logs\guia_icms.txt" logging.basicConfig(filename=path_logs, level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s') def ler_dados_arquivo(caminho_arquivo): dados = {} with open(caminho_arquivo, "r") as file: for linha in file: if "CHAVE DE ACESSO" in linha: dados['chave_acesso'] = linha.split(":")[1].strip() elif "VALOR ICMS" in linha: dados['valor_icms'] = linha.split(":")[1].strip() return dados def preencher_formulario(page, mes_ano_corrente, chave_acesso, nova_data, valor_icms): page.fill('#inscricaoEstadual', "0000000000") ### Inscrição Estadual logging.info("Campo 'inscricaoEstadual' preenchido") page.click('#btnConfirmar') logging.info("Clique em 'Confirmar I.E'") time.sleep(1) page.fill('#periodoReferencia', mes_ano_corrente) logging.info(f"Campo 'periodoReferencia' preenchido com {mes_ano_corrente}") page.click('#tipoVenda2') logging.info("Duplo clique em 'Darf 2'") time.sleep(1) page.click('#tipoVenda2') logging.info("Duplo clique em 'Darf 2'") # Interagir com o campo 'tributo' page.wait_for_selector('#tributo') page.click('#tributo') time.sleep(1) page.select_option('#tributo', value="1512") logging.info("Campo 'tributo' selecionado como Icms normal 1512") page.fill('#nfeNota1', chave_acesso) logging.info(f"Campo 'nfeNota1' preenchido com {chave_acesso}") page.fill('#dataVencimento', nova_data) logging.info(f"Campo 'dataVencimento' preenchido com {nova_data}") page.fill('#valorCampo', valor_icms) logging.info(f"Campo 'valorCampo' preenchido com {valor_icms}") def run(playwright: Playwright) -> None: try: browser = playwright.chromium.launch(headless=False) context = browser.new_context(viewport={"width": 1920, "height": 1080}, accept_downloads=True) page = browser.new_page(java_script_enabled=True) context.set_default_navigation_timeout(20000) page.goto("https://www.sefaz.mt.gov.br/arrecadacao/darlivre/menudarlivre") logging.info("Página inicial carregada") page.click('text="Pessoa Jurídica Inscrita"') logging.info("Clique em 'Pessoa Jurídica Inscrita'") mes_ano_corrente = datetime.now().strftime("%m/%Y") dados = ler_dados_arquivo(r"c:\base\nfe\nfe.txt") chave_acesso = dados.get('chave_acesso', "") valor_icms = dados.get('valor_icms', "") nova_data = (datetime.now() + timedelta(days=1)).strftime("%d/%m/%Y") preencher_formulario(page, mes_ano_corrente, chave_acesso, nova_data, valor_icms) page.click('//*[@id="btnIncluir"]') logging.info("Formulário enviado") ###########IMPRESÃO ################ #Salvar a guia_icms em pdf na sua pasta local ######################## time.sleep(60) #Ativa impressora padrão os.system(ativa_padrao) except TimeoutError as e: logging.error(f"Um erro ocorreu: {e}") finally: context.close() browser.close() logging.info("Navegador fechado") with sync_playwright() as playwright: run(playwright)
Claro! Aqui está o arquivo README.md para o seu projeto no GitHub:

markdown
# Guia ICMS Automatizada

Este projeto automatiza o preenchimento do formulário de guia de ICMS no site da SEFAZ utilizando Python e Playwright.

## Pré-requisitos

- Python 3.7 ou superior
- Playwright
- pywinauto
- keyboard

## Utilização
Configure as impressoras padrão no código:

ativa_pdf: Define a impressora como "Microsoft Print to PDF".

ativa_padrao: Define a impressora como "HP_MFP_M225-M226_PCL_6".

Configure o caminho para os logs:

path_logs: Caminho onde os logs serão salvos.
  
