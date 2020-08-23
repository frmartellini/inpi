# Download de pareceres de patentes do INPI

Este notebook apresenta um código em Python para executar o download dos pareceres de patents da Revista da Propriedade Industrial (RPI) do Instituto Nacional de Propriedade Industrial (INPI), disponíveis semanalmente às terças-feiras.

Pode se fazer a busca por uma edição única ou por várias edições.

## Notas sobre o desenvolvimento

O código foi desenvolvido como parte das atividades de bolsista que o autor desempenha na Agência de Inovação da UNICAMP (Inova), durante o ano de 2020. A aplicação foi desenvolvida parcialmente com base no sistema e-Patentes/Parecer do INPI (*), realizando as chamadas via método GET e protocolo HTTPs através de uma das seguintes urls:

    https://parecer.inpi.gov.br/webservice.php?revista=
    https://parecer.inpi.gov.br/webservice.php?numero=
    http://parecer.inpi.gov.br/download.php?cam=
    
Como o xml disponibilizado pelo sistema e-Patentes/Parecer não apresenta os documentos para todos os códigos de despacho do INPI, optou-se por desenvolver uma aplicação que extraí as informações patentárias diretamente da RPI e comparar o dataframe gerado com um outro criado a partir da listagem dos arquivos no servidor de arquivos do INPI, com base em um código de busca. Para realizar a chamada de donwloads, utilizou-se as especificações do sistema e-Patentes/Parecer.

## Descrição geral das funcionalidades

A primeira parte do notebook gera um relatório com as informações patentárias para uma instituição específica, no formato xlsx (a título de exemplo, foi feita uma busca com a UNICAMP). As informações selecionadas para extração foram:

- o número da edição da RPI
- a data de publicação
- o número de registro de proteção da patente
- o kind code da proteção
- o código de despacho da patente
- o título da patente
- o nome dos titulares
- o nome dos inventores
- a data de depósito
- as informações de classificação internacional

Outras informações patentárias podem ser extraídas da RPI, bastando verificiar a listagem de tags únicas do xml da seção de patentes.

O código também gera uma coluna chamada "Gestão", que indica se o titular responsável é a instituição procurada, ou se esta é um cotitular da patente.

A segunda parte faz o download em si, comparando as informações do relatório de busca gerado anteriormente com as informações do servidor de pareceres do INPI. Para comparar as informações é gerado um código de busca baseado no número da proteção:

Se o número da proteção começar com BR, o código de busca é construído removendo os espaços em branco, o código BR e o dígito identificador.
    
Ex.: BR 10 2015 020353-5 > 102015020353
    
Caso contrário, o código de busca é construído removendo os espaços em branco e o dígito identificador somente.
    
Ex.: PI 0900158-1 > PI0900158
    
## Exceções para o download

O documento do despacho 16.1 (Carta Patente) não se encontra no mesmo servidor de arquivos de pareceres, portanto, neste caso, é necessário fazer o download manualmente no site do INPI.

A RPI pode apresentar mais de um despacho para o mesmo código de proteção. Nas edições analisadas, isso aconteceu somente em duas situações:

1) Um mesmo número de proteção com despachos 15.11 e 6.22, caso em que o documento do parecer encontrado no servidor pertence ao despacho 6.22.

2) Um mesmo número de proteção com despachos 8.7 e 7.5, caso em que o documento do parecer encontrado no servidor pertence ao despacho 7.5.

## Estrura de pastas e nomes dos arquivos

No servidor de arquivos do INPI, o nome dos documentos segue sempre o mesmo padrão:

XX-[CÓDIGO DE BUSCA] + 7 dígitos.pdf

Os dois primeiros digitos correspondem a numeração da quantidade de arquivos daquele parecer (00, 01,...), o código de busca é construído como explicado anteriormente e os 7 digitos estão ligados ao parecer e a edição.

No caso do código desenvolvido, optou-se por mudar o nome do arquivo no momento do download, adotando o seguinte formato para o nome dos arquivos:

[NÚMERO DA PROTEÇÃO]\_Despacho [X.XX]_[DATA DA RPI]_[CONTADOR ALFABÉTICO].pdf

Ex.:  PI 1104516-7_Despacho 7.1_180820_A.pdf

O contador alfabético não é uma prática muito comum, mas se tratou de um excelente exercício de programação, e pode ser substituído por um contador numérico sem prejuízo.
 
No caso das pastas, foi utilizado um padrão em três níveis, um principal (raiz) e dois secundários para cada patente:

PareceresRPI/[NÚMERO DA PROTEÇÃO]/RPI[NÚMERO]_[DATA]

Ex.: PareceresRPI/BR 10 2014 021620-0/RPI2589_180820

# Versões dos módulos utilizados

- Python v. 3.7.7
- Jupyter Notebook Server v. 6.0.3
- Requests v. 2.23.0
- Pandas v. 1.0.5
- Numpy v. 1.18.5
- BeautifulSoup4 v. 4.9.1
- XlsxWriter v. 1.2.9
- Zipfile v. 3.1.0

## Links úteis

- Sistema  e-Patentes/Parecer - <http://antigo.inpi.gov.br/noticias/inpi-lanca-sistema-e-patentes-parecer>
- Edições da Revista de Propriedade Industrial - <http://revistas.inpi.gov.br/rpi/>