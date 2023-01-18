> Olá caros programadores 😁, ainda estou escrevendo este documento, por favor aguardem mais alguns dias para que eu possa finalizar.<br><br>Atenciosamente, **Eduardo Baginski Costa**.

[github]: https://github.com/eduardobaginskicosta
[facebook]: https://facebook.com/eduardobaginskicosta/
[twitter]: https://twitter.com/eduardobcosta7/
[instagram]: https://instagram.com/eduardobcosta7/
[medium]: https://eduardobcosta.medium.com/
[youtube]: https://youtube.com/@eduardobcosta/
[linkedin]: https://www.linkedin.com/in/eduardobaginskicosta/
[ansi]: https://pt.wikipedia.org/wiki/American_National_Standards_Institute

<h1 align="center">ANSI Escape Code</h1>

Olá caros programadores, eu sou [Eduardo Baginski Costa](github) um autodidata e programador brasileiro.

Através desse repositório irei apresentar utilizações básicas dos **códigos de escape [ANSI](ansi) (ANSI Escape Codes)** para formatar saídas e entradas de texto em terminais baseados nas normas **ANSI** e **ANSI X3.64**.

Os códigos **[ANSI](ansi)** podem não suportados de maneira nativa por algumas linguagens de programação, como o **CSharp**, sendo necessário a implementação de métodos externos, geralmente da API do Windows **Kernel32**.

<h2 align="right">Avisos Importantes</h2>

Como mencionado previamente, os códigos de escape **[ANSI](ansi)** não são suportados de maneira nativa por todas as linguagens de programação, e por esse e outros motivos recomendo uma busca rápida de como fornecer suporte a esses códigos.

Algumas linguagens de programação, como o **C++**, apresentam suportes nativos aos códigos de escape **[ANSI](ansi)** não necessitando de bibliotecas e métodos externos. Como um programador amante do **CSharp** deixarei a seguir como habilitar esse suporte utilizando uma API do Windows, o **Kernel32**.

``````csharp
using System.
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
Console.WriteLine(
	string.Format("\x1B[1;31m{0}", "Parágrafo negrito e vermelho.")
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
| **`ESC[{line};{column}H`**<br>**`ESC[{line};{column}`**f | Movimentar o cursor do terminal para a linha **X** (`{line}`) e para a coluna **Y** (`{column}`). |
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

<h3 align="center">•&ensp;Formatação de Texto ( Modo Gráfico )&ensp;•</h3>

Acompanhe a seguir a tabela de códigos de escape **[ANSI](ansi)** para realizar a formatação dos caracteres (modo gráfico).

**IMPORTANTE :** algumas das sequências podem não ser suportados por alguns terminais dependendo do sistema operacional e sua versão, ou da versão do emulador (se esse for o caso).

**IMPORTANTE :** as formatações **dim** e **bold** são redefinidos com a sequência `ESC[22m`. A sequência `ESC[22m` é uma sequência não especificada para a formatação chamada de **sublinhado duplo**, podendo somente funcionar em alguns terminais e pode ser redefinida com a sequência `ESC[24m`.

| **Sequência de Código ESC** | **Redefinir a Sequência** | **Descrição**                                                |
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
