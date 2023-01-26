[github]: https://github.com/eduardobaginskicosta
[facebook]: https://facebook.com/eduardobaginskicosta/
[twitter]: https://twitter.com/eduardobcosta7/
[instagram]: https://instagram.com/eduardobcosta7/
[medium]: https://eduardobcosta.medium.com/
[youtube]: https://youtube.com/@eduardobcosta/
[linkedin]: https://www.linkedin.com/in/eduardobaginskicosta/
[ansi]: https://pt.wikipedia.org/wiki/American_National_Standards_Institute
[aixterm]: https://sites.ualberta.ca/dept/chemeng/AIX-43/share/man/info/C/a_doc_lib/cmds/aixcmds1/aixterm.htm
[trueColor]: https://en.wikipedia.org/wiki/Color_depth#True_color_.2824-bit.29
[xterm]: https://invisible-island.net/xterm/ctlseqs/ctlseqs.html
[keyboardStrings]: https://gist.github.com/fnky/458719343aabd01cfb17a3a4f7296797#list-of-keyboard-strings

<h1 align="center">ANSI Escape Code</h1>

<p float="left">
	<a href="./ANSI Escape Codes.pdf" download>
		<img src="https://img.shields.io/badge/Baixar_Arquivo_PDF-F40F02?style=for-the-badge&logo=Adobe&logoColor=white" title="Baixar arquivo PDF.">
	</a>
	<a href="./256-colors.jpg" download>
		<img src="https://img.shields.io/badge/Baixar_Imagem_da_Tabela-1a73e8?style=for-the-badge" title="Baixar imagem da Tabela.">
	</a>
</p>

Olá caros programadores, eu sou [Eduardo Baginski Costa](github) um autodidata e programador brasileiro.

Através desse repositório irei apresentar utilizações básicas dos **códigos de escape [ANSI](ansi) (ANSI Escape Codes)** para formatar saídas e entradas de texto em terminais baseados nas normas **ANSI** e **ANSI X3.64**.

Os códigos **[ANSI](ansi)** podem não suportados de maneira nativa por algumas linguagens de programação, como o **CSharp**, sendo necessário a implementação de métodos externos, geralmente da API do Windows **Kernel32**.

<h2 align="right">Avisos Importantes</h2>

Como mencionado previamente, os códigos de escape **[ANSI](ansi)** não são suportados de maneira nativa por todas as linguagens de programação, e por esse e outros motivos recomendo uma busca rápida de como fornecer suporte a esses códigos.

Algumas linguagens de programação, como o **C++**, apresentam suportes nativos aos códigos de escape **[ANSI](ansi)** não necessitando de bibliotecas e métodos externos. Como um programador amante do **CSharp** deixarei a seguir como habilitar esse suporte utilizando uma API do Windows, o **Kernel32**.

``````csharp
using System;
using System.Runtime.InteropServices;

namespace ANSIEscapeCodes
{
  #region Métodos Importados do User32
  
  [DllImport("kernel32.dll")]
  private static extern bool GetConsoleMode(IntPtr hConsoleHandle, out uint lpMode);
  
  [DllImport("kernel32.dll")]
  private static extern bool SetConsoleMode(IntPtr hConsoleHandle, uint dwMode);
  
  [DllImport("kernel32.dll", SetLastError = true)]
  private static extern IntPtr GetStdHandle(int nStdHandle);
  
  #endregion
  
  public static void Main(string[] args)
  {
    #region Habilitar o ANSI Escape Codes
    
    var outputMode = (uint)0;
    GetConsoleMode(GetStdHandle(-11), out outputMode);
    outputMode |= 0x0004 | 0x0008;
    SetConsoleMode(GetStdHandle(-11), outputMode);
    
    #endregion
    
    Console.ReadKey(true);
  }
}
``````

De uma maneira resumida, este código importa métodos externos do **Kernel32** para habilitar esse suporte, sendo estes o `GetConsoleMode` (exporta os modos aplicados à uma janela), o `SetConsoleMode` (aplica novos modos à uma janela) e o `GetStdHandle` (captura o controlador de uma janela).  A importação é realizada através do `DLLImport` disponibilizado pelo **namespace** `System.Runtime.InteropServices`.

-   `0x0004` : este modo ordena ao terminal que antes de uma sequência de caracteres sejam exibidos a um usuário, estes devem ser antes analisados para o **VT100** e para as **sequências de caracteres de controle**, ou seja, as sequências de caracteres especiais que podem movimentar o mouse, alterar as colorações, alterar as fontes, entre outras operações que podem ser executadas através da API do terminal.
-   `0x0008` : este modo é destinado à utilização conjunta ao modo `0x0004` para melhorar a emulação do terminal, informando que quando um caractere for exibido no final da tela (canto inferior direito) a rolagem imediata deve ser desabilitada, sendo este um comportamento desejado.

<h2 align="right">Utilização Básica</h2>

A partir daqui iremos aprender a utilizar os códigos de escape **[ANSI](ansi)** para realizar a formatação de textos no terminal da maneira que for desejada.

Primeiramente, os códigos de escape **[ANSI](ansi)** padrão são prefixados com o chamado `ESC` ou `Escape`, que são utilizados para indicar ao terminal que a sequência seguinte de caracteres devem ser analisadas e interpretadas, não as exibindo ao usuário.

No total temos cinco prefixos para os códigos de escape **[ANSI](ansi)**, e estes são:

-   **Control-Key** : `^[`.
-   **Octal** : `\033`.
-   **Unicode** : `\u001b`.
-   **Hexadecimal** : `\x1B`.
-   **Decimal** : `27`.

Os mesmos são seguidos pelos comandos que devem ser executados pelo terminal, sendo algumas vezes (não necessariamente) pela abertura do **colchete** (`[`), também conhecido como **Introdutor de Sequência do Controle (CSI)**, que opcionalmente pode ser seguido de argumentos ou pelo próprio comando.

Cada um dos comandos são delimitados, ou seja, separados pelo **ponto e vírgula** (`;`), como mostrado nos exemplos a seguir.

```bash
\x1B[1;31m # O parágrafo será escrito em negrito e vermelho
```

```csharp
// Escrevemos o texto em negrito e com sua cor sendo vermelho
Console.WriteLine(
	string.Format("\x1B[1;31m{0}", "Parágrafo em negrito e vermelho.")
);
```

<h3 align="center">•&ensp;Sequências do Código de Escape ANSI&ensp;•</h3>

As sequências utilizadas pelas códigos de escape **[ANSI](ansi)** são:

-   **`ESC`** : sequência começando com `ESC` (`\x1B`).
-   **`CSI` (Introdutor de Sequência de Controle)** : sequência começando com `ESC [` ou **CSI** (`\x9B`).
-   **`DCS` (Cadeia de Controle do Dispositivo)** : sequência começando com `ESC P` ou **DCS** (`\x90`).
-   **OSC (Comando do Sistema Operacional)** : sequência começando com `ESC ]` ou **OSC** (`\x9D`).

Qualquer espaço em branco entre as sequências e argumentos devem ser ignorados durante a sua utilização, pois estes estão presentes para fins didáticos e de legibilidade.

<h3 align="center">•&ensp;Tabela dos Códigos ASCII Gerais&ensp;•</h3>

Acompanhe na sequência a tabela dos códigos **ASCII** gerais utilizados nos códigos de escape **[ANSI](ansi)**.

**IMPORTANTE :** algumas das sequências de escape de controle, tal como o `\e` para o `ESC`, não possuem garantia de funcionarem para todos os idiomas e compiladores, ou seja, é recomendado a utilização dos seus representantes em **decimal**, **octal** ou **hexadecimal** como código de escape.

**IMPORTANTE :** a representação da **Tecla Ctrl** é utilizado apenas para associar os caracteres não imprimíveis do código **ASCII 1** com os caracteres imprimíveis do código **ASCII 65** ("A"). No código **ASCII 1** seria `^A` (Ctrl + A), enquanto no código **ASCII 7 (Bel)** seria `^G` (Ctrl + G).

| **Nome**  | **Decimal** | **Octal** | **Hexadecimal** | **Escape C** | **Tecla Ctrl** | **Descrição**                    |
| --------- | ----------- | --------- | --------------- | ------------ | -------------- | -------------------------------- |
| **`BEL`** | 7           | 007       | 0x07            | `\a`         | `^G`           | Sino do terminal.                |
| **`BS`**  | 8           | 010       | 0x08            | `\b`         | `^H`           | Backspace.                       |
| **`HT`**  | 9           | 011       | 0x09            | `\t`         | `^I`           | Tabulação horizontal.            |
| **`LF`**  | 10          | 012       | 0x0A            | `\n`         | `^J`           | Avançar uma linha (nova linha).  |
| **`VT`**  | 11          | 013       | 0x0B            | `\v`         | `^K`           | Tabulação vertical.              |
| **`FF`**  | 12          | 014       | 0x0C            | `\f`         | `^L`           | Nova página.                     |
| **`CR`**  | 13          | 015       | 0x0D            | `\r`         | `^M`           | Retornar ao início da linha.     |
| **`ESC`** | 27          | 033       | 0x1B            | `\e`         | `^[`           | Caractere de escape ( **ESC** ). |
| **`DEL`** | 127         | 177       | 0x7F            | `<none>`     | `<none>`       | Apagar um caractere.             |

<h3 align="center">•&ensp;Controles do Cursor&ensp;•</h3>

Acompanhe na sequência a tabela dos códigos de escape **[ANSI](ansi)** para controlar o cursor do terminal.

**IMPORTANTE :** algumas das sequências são particulares de cada sistema operacional, podendo inclusive alterar dependendo da versão do sistema utilizado, ou seja, não são sequências padronizadas, tal como as sequência para **salvar** e **restaurar** os cursores. Alguns emuladores de terminais (**xterm** ou derivados) possuem suporte para as sequências **SCO** e **DEC**, mas provavelmente possam possuir funcionalidades diferentes. Recomendo a utilização das sequências **DEC**.

| **Sequência do Código ESC**                              | **Descrição**                                                |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| **`ESC[H`**                                              | Movimentar o cursor do terminal para a posição inicial (0, 0). |
| **`ESC[{line};{column}H`**<br/>**`ESC[{line};{column}f`**| Movimentar o cursor do terminal para a linha **X** (`{line}`) e para a coluna **Y** (`{column}`). |
| **`ESC[{amount}A`**                                      | Movimentar o cursor do terminal para cima **Y** (`{amount}`) linhas. |
| **`ESC[{amount}B`**                                      | Movimentar o cursor do terminal para baixo **Y** (`{amount}`) linhas. |
| **`ESC[{amount}C`**                                      | Movimentar o cursor do terminal para a direita **X** (`{amount}`) colunas. |
| **`ESC[{amount}D`**                                      | Movimentar o cursor do terminal para a esquerda **X** (`{amount}`) colunas. |
| **`ESC[{amount}E`**                                      | Movimentar o cursor do terminal para o início da linha à **Y** (`{amount}`) linhas para baixo. |
| **`ESC[{amount}F`**                                      | Movimentar o cursor do terminal para o início da linha anterior à **Y** (`{amount}`) linhas. |
| **`ESC[{amount}G`**                                      | Movimentar o cursor do terminal para a coluna **X** (`{amount}`). |
| **`ESC[6n`**                                             | Solicitar a posição do cursor do terminal com a resposta sendo devolvido como `ESC[{X},{Y}R`. |
| **`ESC M`**                                              | Movimentar o cursor do terminal uma linha para cima (rolando caso necessário). |
| **`ESC 7`**                                              | Salvar a posição atual do cursor do terminal ( **DEC** ).    |
| **`ESC 8`**                                              | Restaura a posição do cursor do terminal para a última posição salva ( **DEC** ). |
| **`ESC[s`**                                              | Salvar a posição atual do cursor do terminal ( **SCO** ).    |
| **`ESC[u`**                                              | Restaura a posição do cursor do terminal para a última posição salva ( **SCO** ). |

<h3 align="center">•&ensp;Funções de Apagamento&ensp;•</h3>

Acompanhe na sequência a tabela dos códigos de escape **[ANSI](ansi)**  para apagar caracteres no terminal.

**IMPORTANTE :** e eliminação de uma linha não movimenta o cursor do terminal, ou seja, irá permanecer na mesma posição antes do caractere ter sido apagado. Pode ser utilizado `\r` após apagar uma linha para que o cursor do terminal seja movimentado até o início da linha apagada.

| **Sequência do Código ESC** | **Descrição**                                                |
| --------------------------- | ------------------------------------------------------------ |
| **`ESC[J`**                 | Apagar a tela de exibição (área visível) do terminal.        |
| **`ESC[0J`**                | Apagar a partir do cursor do terminal até o final da tela de exibição. |
| **`ESC[1J`**                | Apagar a partir do início da tecla de exibição até o cursor do terminal. |
| **`ESC[2J`**                | Apagar a tela inteira do terminal (equivalente ao comando `cls`). |
| **`ESC[3J`**                | Apagar as linhas salvas.                                     |
| **`ESC[K`**                 | Apagar na linha (equivalente ao `ESC[0K`).                   |
| **`ESC[0K`**                | Apagar a partir do cursor do terminal até o final da linha atual. |
| **`ESC[1K`**                | Apagar a partir do início da linha atual até o cursor do terminal. |
| **`ESC[2K`**                | Apagar a linha atual inteira.                                |

<h3 align="center">•&ensp;Formatação dos Caracteres ( Modo Gráfico )&ensp;•</h3>

Acompanhe a seguir a tabela de códigos de escape **[ANSI](ansi)** para realizar a formatação dos caracteres (modo gráfico).

**IMPORTANTE :** algumas das sequências podem não ser suportados por alguns terminais dependendo do sistema operacional e sua versão, ou da versão do emulador (se esse for o caso).

**IMPORTANTE :** as formatações **dim** e **bold** são redefinidos com a sequência `ESC[22m`. A sequência `ESC[22m` é uma sequência não especificada para a formatação chamada de **sublinhado duplo**, podendo somente funcionar em alguns terminais e pode ser redefinida com a sequência `ESC[24m`.

| **Sequência do Código ESC** | **Redefinir a Sequência** | **Descrição**                                                |
| --------------------------- | ------------------------- | ------------------------------------------------------------ |
| **`ESC[1;34;{...}m`**       | `<none>`                  | Define os gráficos para célula (separador por `;`).          |
| **`ESC[0m`**                | `<none>`                  | Redefine todos os modos gráficos (estilos e cores) ao padrão do terminal. |
| **`ESC[1m`**                | **`ESC[22m`**             | Formata os caracteres para o modo negrito ( **bold** ).      |
| **`ESC[2m`**                | **`ESC[22m`**             | Formata os caracteres para o modo escurecido ( **dim** ).    |
| **`ESC[3m`**                | **`ESC[23m`**             | Formata os caracteres para o modo itálico ( **italic** ).    |
| **`ESC[4m`**                | **`ESC[24m`**             | Formata os caracteres para o modo sublinhado ( **underline** ). |
| **`ESC[5m`**                | **`ESC[25m`**             | Formata os caracteres para o modo cintilante / piscando ( **blinking** ). |
| **`ESC[7m`**                | **`ESC[27m`**             | Formata os caracteres para o modo inverso / reverso ( **inverse** ). |
| **`ESC[8m`**                | **`ESC[28m`**             | Formata os caracteres para o modo oculto / invisível ( **hidden** ). |
| **`ESC[9m`**                | **`ESC[29m`**             | Formatar os caracteres para o modo tachado ( **strikethrough** ). |

<h3 align="center">•&ensp;Coloração ( Códigos de Cores )&ensp;•</h3>

A grande maioria dos terminais suportam a utilização de **8** e **16 cores**, bem como cores de **256** (8 bits). Essas cores são definidas pela usuário, porém tem significados comumente definidos.

<h4 align="center">—&ensp;8-16 Cores&ensp;—</h4>

Acompanhe a seguir a tabela dos códigos de escape **[ANSI](ansi)** das cores básicas do terminal para alterar a coloração do texto e do fundo os caracteres.

**IMPORTANTE :** o código de escape para a redefinição não serve somente para as cores, mas também para a formatação dos caracteres. Por esse motivo recomendo a utilização dos códigos de cores para voltar a cor desejada.

| **Nome da Cor** | **Códigos de Cores do Primeiro Plano** | **Código de Cores de Fundo** |
| --------------- | -------------------------------------- | ---------------------------- |
| **Preto**       | `30`                                   | `40`                         |
| **Vermelho**    | `31`                                   | `41`                         |
| **Verde**       | `32`                                   | `42`                         |
| **Amarelo**     | `33`                                   | `43`                         |
| **Azul**        | `34`                                   | `44`                         |
| **Magenta**     | `35`                                   | `45`                         |
| **Ciano**       | `36`                                   | `46`                         |
| **Branco**      | `37`                                   | `47`                         |
| **Padrão**      | `39`                                   | `49`                         |
| **Redefinir**   | `0`                                    | `0`                          |

Grande parte dos terminais suportam cores "brilhantes" ou "negrito" indo além do conjunto básico de **8 cores**, e estes possuam seu próprio conjunto de códigos se espelhando nas cores normais, porém com o adicional de conter `;1` em seus códigos, como mostra o exemplo a seguir:

```bash
# Definimos o estilo do parágrafo como negrito e vermelho
\x1B[1;31mHello

# Definimos o estilo do parágrafo como branco esmaecido com o fundo vermelho
\x1B[2;37;41mWorld
```

```csharp
// Escrevemos o texto em negrito e com sua cor sendo vermelho
Console.WriteLine(
	string.Format("\x1B[1;31m{0}", "Hello")
);

// Escrevemos a cor como branco esmaecido com o fundo vermelho
Console.WriteLine(
	string.Format("\x1B[2;37;41m{0}", "World.")
);
```

Os terminais que suportam a **[especificação aixterm](aixterm)** fornecem versão brilhantes das cores **ISO** sem a necessidade de usar o modificador em negrito. Acompanhe  a tabela a seguir:

| **Nome da Cor**        | **Códigos de Cores do Primeiro Plano** | **Códigos de Cores de Fundo** |
| ---------------------- | -------------------------------------- | ----------------------------- |
| **Preto brilhante**    | `90`                                   | `100`                         |
| **Vermelho brilhante** | `91`                                   | `101`                         |
| **Verde brilhante**    | `92`                                   | `102`                         |
| **Amarelo brilhante**  | `93`                                   | `103`                         |
| **Azul brilhante**     | `94`                                   | `104`                         |
| **Magenta brilhante**  | `95`                                   | `105`                         |
| **Ciano brilhante**    | `96`                                   | `106`                         |
| **Branco brilhante**   | `97`                                   | `107`                         |

<h4 align="center">—&ensp;256 Cores&ensp;—</h4>

Os códigos de escape **[ANSI](ansi)** a seguir servem para informar ao terminal para utilizar o **ID** da cor fornecida.

| **Sequência do Código ESC** | **Descrição**                                   |
| --------------------------- | ----------------------------------------------- |
| **`ESC[38;5{id}m`**         | Definir a cor do primeiro plano (cor do texto). |
| **`ESC[48;5{id}m`**         | Definir a cor do plano de fundo.                |

O parâmetro `{id}` deve ser substituído por um dos índices das cores que vão de **0** até **255** mostrados na tabela a seguir:

![Tabela de 256 Cores](./256-colors.jpg)

A tabela acima começa com as **16 cores** originais (0 até o 15). Os processos das **216 cores** (16 até o 231) ou formados por um valor **RGB** de **3bpc** compensados por **16**, são compactadas em um valor único.

As **24 cores** finais (232 até o 255) são a escala de cinza, que começa por uma sombra ligeiramente mais clara que o preto, e vai variando até uma sombra um pouco mais escura que o branco.

Alguns dos emuladores de terminais interpretam essas etapas como **incrementos lineares** (`256 / 24`) nos três canais, mas alguns emuladores podem definir explicitamente esses valores.

<h4 align="center">—&ensp;Cores RGB&ensp;—</h4>

Os terminais mais modernos suportam o **[Truecolor](trueColor)** (RGB de 24 bits) permitindo que as cores do primeiro plano (cor do texto) e do plano de fundo possam ser definidos utilizando os códigos **RGB**.

As sequências dos códigos de escape **[ANSI](ansi)** relacionadas ao **[Truecolor](trueColor)** (de uma maneira geral) não são bem documentadas.

Acompanhe a seguir a tabela dos códigos de escape **[ANSI](ansi)** para definir a coloração do primeiro plano e do plano de fundo utilizando o código **RGB**:

| **Sequência do Código ESC** | **Descrição**                                                |
| --------------------------- | ------------------------------------------------------------ |
| **`ESC[38;2;{r};{g};{b}m`** | Define o código RGB a ser utilizado no primeiro plano (cor do texto). |
| **`ESC[48;2;{r};{g};{b}m`** | Define o código RGB a ser utilizado no plano de fundo.       |

**IMPRTANTE :** observe que os argumentos `;38` e `;48` correspondem à sequência de **16 cores** sendo interpretados pelo terminal como definição das cores do primeiro e segundo plano (respectivamente), onde o `;2` e `;5` definem o formato da cor.

<h3 align="center">•&ensp;Modos de Tela&ensp;•</h3>

A seguir iremos acompanhar diversas tabelas contendo os códigos de escape **[ANSI](ansi)** para realizar formatações e configurações na tela da maioria dos terminais modernos.

<h4 align="center">—&ensp;Modo de Configuração&ensp;—</h4>

Acompanhe a seguir a tabela dos códigos de escape **[ANSI](ansi)** para alterar as configurações de tela do terminal.

| **Sequência do Código ESC** | **Descrição**                                                |
| --------------------------- | ------------------------------------------------------------ |
| **`ESC[={value}h`**         | Altera a largura ou o tipo da tela para o modo especificado pelo valor (`{value}`). |
| **`ESC[=0h`**               | Define as configurações da tela para 40 x 25 monocromático ( texto ). |
| **`ESC[=1h`**               | Define as configurações da tela para 40 x 25 colorido ( texto ). |
| **`ESC[=2h`**               | Define as configurações da tela para 80 x 25 monocromático ( texto ). |
| **`ESC[=3h`**               | Define as configurações da tela para 80 x 25 colorido ( texto ). |
| **`ESC[=4h`**               | Define as configurações da tela para 320 x 200 de 4 colorido ( gráficos ). |
| **`ESC[=5h`**               | Define as configurações da tela para 320 x 200 monocromático ( gráficos ). |
| **`ESC[=6h`**               | Define as configurações da tela para 640 x 200 monocromático ( gráficos ). |
| **`ESC[=7h`**               | Habilita a quebra de linhas.                                 |
| **`ESC[=13h`**              | Define as configurações da tela para 320 x 200 colorido ( gráficos ). |
| **`ESC[=14h`**              | Define as configurações da tela para 640 x 200 cores ( gráficos de 16 cores ). |
| **`ESC[=15h`**              | Define as configurações da tela para 640 x 250 monocromático ( gráficos de 2 cores ). |
| **`ESC[=16h`**              | Define as configurações da tela para 640 x 350 colorido ( gráficos de 16 cores ). |
| **`ESC[=17h`**              | Define as configurações da tela para 640 x 480 monocromático ( gráficos de 2 cores ). |
| **`ESC[=18h`**              | Define as configurações da tela para 640 x 480 colorido ( gráficos de 16 cores ). |
| **`ESC[=19h`**              | Define as configurações da tela para 320 x 200 colorido ( gráficos de 256 cores ). |
| **`ESC[={value}l`**         | Redefine as configurações da tela ao normal utilizando os mesmos valores (`{values}`), com exceção do número 7 que desativa a quebra de linha. O último caractere utilizando nesta sequência de escape **[ANSI](ansi)** é um L ( minúsculo ). |

<h4 align="center">—&ensp;Modos Privados Comuns&ensp;—</h4>

Alguns dos códigos de escape **[ANSI](ansi)** podem ser privados, ou seja, somente funcionam ao determinados sistemas operacionais, e em determinadas versões dos terminais, e este é um desses casos.

Acompanhe a seguir a tabela dos códigos de escape **[ANSI](ansi)** para alterar as configurações da tela do terminal, porém com sequências privadas, ou seja, que não são definidos pelas especificações dos códigos de escape **[ANSI](ansi)**, mas são implementados pela maioria dos terminais.

**IMPORTANTE :** mesmo que esses modos possam ser suportados pela grande maioria dos terminais, alguns podem não funcionar em multiplexadores como o **tmux**.

| **Sequência do Código ESC** | **Descrição**                                |
| --------------------------- | -------------------------------------------- |
| **`ESC[?25l`**              | Torna o cursor do terminal invisível.        |
| **`ESC[?25h`**              | Torna o cursor do terminal visível.          |
| **`ESC[?47l`**              | Restaura a tela do terminal.                 |
| **`ESC[?47h`**              | Salva a tela do terminal.                    |
| **`ESC[?1049h`**            | Habilita o buffer alternativo do terminal.   |
| **`ESC[?1049l`**            | Desabilita o buffer alternativo do terminal. |

Recomendo a leitura das [sequências de controle XTerm](xterm), uma lista mais aprofundada de modos privados definidos pelo **XTerm**.

<h4 align="center">—&ensp;Sequências de Teclado&ensp;—</h4>

Redefina uma chave do teclado para uma sequência especificada através dos parâmetros utilizados na seguinte sequência de escape:

```bash
ESC[{code};{string};{...}p
```

-   **`code`** : é um ou mais valores listados na [lista de sequências de teclado](keyboardStrings). Esses valores representam as chaves do teclado e combinações de chaves. Ao usar esses valores em algum comando, você deve digitar os ponto e vírgulas mostrados na tabela além dos exigidos pelas sequências de escape **[ANSI](ansi)**. Os códigos entre parênteses não estão disponíveis em alguns teclados. O `ANSI.SYS` não irá interpretar os códigos entre parênteses para esses teclados, a menos que seja especificado a opção `/x` no comando `DEVICE` para o `ANSI.SYS`.
-   **`string`** : é o código **ASCII** para um único caractere ou uma sequência contida entre aspas, como por exemplo, **65** e **"A"** podem ser utilizados para representar um **A** maiúsculo.

**IMPORTANTE :** alguns dos valores contidos na [lista de sequências de teclado](keyboardStrings) não são válidos para todos os computadores. Recomendo a verificação da documentação do seu computador quanto aos valores diferentes.

<h3 align="center">•&ensp;Recursos, Referências e Fontes&ensp;•</h3>

Acompanhe a seguir os links externos para sites, documentos e publicações utilizadas para a escrita desse repositório referente aos códigos de escape **[ANSI](ansi)**. 

Deixo aqui os agradecimentos especiais ao [Christian Petersen](https://github.com/fnky) e a sua [publicação original](https://gist.github.com/fnky/458719343aabd01cfb17a3a4f7296797) no **GitHub**.

-   [Wikipedia: código de escape ANSI](https://en.wikipedia.org/wiki/ANSI_escape_code).
-   [Crie sua própria linha de comando com códigos de escape ANSI](http://www.lihaoyi.com/post/BuildyourownCommandLinewithANSIescapecodes.html).
-   [Tabela ASCII: sequências de escape ANSI](http://ascii-table.com/ansi-escape-sequences.php).
-   [Bluesock: códigos ANSI](https://bluesock.org/~willkg/dev/ansi.html).
-   [Hackers: introdução de códigos de terminal ( ANSI / VT100 )](http://wiki.bash-hackers.org/scripting/terminalcodes).
-   [Sequências de controle XTerm](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html).
-   [VT100 – Vários manuais de terminal](https://vt100.net/).
-   [xterm.js – Sequências de terminais suportados](https://xtermjs.org/docs/api/vtfeatures/).
