[github]: https://github.com/eduardobaginskicosta
[facebook]: https://facebook.com/eduardobaginskicosta/
[twitter]: https://twitter.com/eduardobcosta7/
[instagram]: https://instagram.com/eduardobcosta7/
[medium]: https://eduardobcosta.medium.com/
[youtube]: https://youtube.com/@eduardobcosta/
[linkedin]: https://www.linkedin.com/in/eduardobaginskicosta/
[ansi]: https://pt.wikipedia.org/wiki/American_National_Standards_Institute

<!-- INTRODUÇÃO -->

<h1 align="center">ANSI Escape Codes</h1>

Olá caros programadores, eu sou [Eduardo Baginski Costa](github) um autodidata e um programador brasileiro.   
Através desse repositório irei apresentar exemplos de utilização do **ANSI Escape Codes (Códigos de escape [ANSI](ansi))** para formatar
saídas e entradas de texto em terminais. Os códigos **[ANSI](ansi)** podem não ser suportados de maneira nativa por algumas linguagens de
programação, como o **CSharp**, sendo necessário a implementação de métodos externos da biblioteca **Kernel32**.

<!-- AVISOS -->

<h2 align="right">Avisos Importantes</h2>

Como mencionado anteriormente os códigos de escape **[ANSI](ansi)** podem não ser suportados nativamente por algumas linguagens de programação,
como o **CSharp**. Por esses e outros motivos recomendo que uma busca rápida seja realizada de como habilitar o suporte a esses códigos na
linguagem de programação utilizada.   
   
A linguagem **C++** (ou **Cpp**, **C-Plus-Plus**) apresenta suporte nativo aos códigos de escape **[ANSI](ansi)**, e não necessita de métodos
e bibliotecas externas. Como um programador que ama o **CSharp** deixarei a seguir os **namespaces** e os métodos externos importados da **DLL** do
**Kernel32** para habilitar o suporte a esses códigos.

```csharp
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
```

Basicamente importamos os métodos externos ```GetConsoleMode```, ```SetConsoleMode``` e ```GetStdHandle``` da biblioteca **Kernel32** através
do ```DllImport``` disponibilizado pelo **namespace** ```System.Runtime.InteropServices```.   
   
Através desses métodos utilizamos o ```GetStdHandle``` para exportar através o método ```GetConsoleMode``` os modos atrelados a janela atual do
terminal para a variável ```outputMode```. Em seguida adicionamos dois modos extras o ```0x0004``` e o ```0x0008``` e os aplicamos novamente a
janela atual do console através do método ```SetConsoleMode```.

- ```0x0004```: quando ordenamos ao terminal para que exibida uma sequência de caracteres, antes deles serem exibidos ao usuário eles serão analisados para o **VT100** e para as **sequências de caracteres de controle**, ou seja, caracteres especiais que podem movimentar o mouse, alterar as colorações, as fontes, além de outras operações executadas através da API do terminal.

- ```0x0008```: seu uso é destinado a utilização conjunta ao ```0x0004``` para melhorar a emulação do terminal, informando que quando um caractere é escrito no final da tela (canto inferior direito) a rolagem imediata deve ser desabilitada, sendo este o comportamento desejado.

<!-- BÁSICO -->

<h2 align="right">Utilização Básica</h2>

Agora iremos aprender a utilizar os códigos de espace **[ANSI](ansi)** para formatar os textos do terminal como desejarmos.   
Primeiramente, os códigos de escape **[ANSI](ansi)** padrão são prefixados com o chamado ```ESC``` ou ```Escape```, que são utilizados para
indicar ao terminal que o código deve ser interpretado e não exibido ao usuário.   
   
No total, temos cinco prefixos para os códigos de escape **[ANSI](ansi)**, e eles são:
- Control-Key: ```^[```.
- Octal: ```\033```.
- Unicode: ```\u001b```.
- Hexadecimal: ```\x1B```.
- Decimal: ```27```.

Estes são seguidos pelos comandos que devem ser executados pelo terminal, sendo algumas vezes delimitados pela abertura do colchete (```[```),
conhecido como **Introdutor de Sequência do Controle (CSI), que opcionalmente pode ser seguido de argumentos ou pelo próprio comando.   
   
Os comandos são delimitados, ou seja, separados pelo ponto e vírgula (```;```), como mostra o exemplo a seguir:
```sh
\x1B[1;31m # Aqui foi definido que o parágrafo deve ser escrito em negrito e com o texto em vermelho
```
```csharp
[...]
Console.WriteLine("\x1B[1;32mIt's a bold and red paragraph!");
Console.ReadKey(true);
[...]
```

<h3 align="center">Sequências do código de escape ANSI</h3>

As sequências utilizadas pelos códigos de escape **[ANSI](ansi)** são:
- ```ESC``` : sequência começando com ```ESC``` (```\x1B```).
- ```CSI``` : **Introdutor de Sequência de Controle**: sequência começando com ```ESC [``` ou **CSI** (```\x9B```).
- ```DCS``` : **Cadeia de Controle do Dispositivo**: sequêcia começando ```ESC P``` ou **DCS** (```\x90```).
- ```OSC``` : **Comando do Sistema Operacional**: sequência começando com ```ESC ]``` ou **OSC** (```\x9D```).

Qualquer espaço em branco entre as sequências e argumentos devem ser ignorados na hora de sua uilização.   
Estes espaços estão presentes para melhorar a legibilidade.

<h3 align="center">Tabela dos códigos ASCII gerais</h3>

A seguir, acompanhe a tabela dos códigos **ASCII** gerais utilizados no código de escape **[ANSI](ansi)**.   
   
**Importante:** algumas das sequências de escape de controle, como o ```\e``` para o ```ESC```, não tem garantia de funcionarem para todos os
idiomas e compiladores, ou seja, recomendo a utilização do seu representante em **decimal**, **octal** ou **hexadecimal** como código de escape.
   
**Importante:** A representação da **Tecla Ctrl** é utilizado apénas para associar os caracteres não imprimíveis do código **ASCII 1** com os
caracteres imprimiveis do código **ASCII 65** ("A"). No código **ASCII 1** seria ```^A``` (Ctrl + A), enquanto no código **ASCII 7 (Bel)** seria
```^G``` (Ctrl + G).

|**Nome**|**Decimal**|**Octal**|**Hexadecimal**|**Escape C**|**Tecla Ctrl**|**Descrição**|
| ----- | ------- | ----- | ---- | -------- | -------- | ------------------------------- |
| `BEL` | 7       | 007   | 0x07 | `\a`     | `^G`     | Sino do terminal.               |
| `BS`  | 8       | 010   | 0x08 | `\b`     | `^H`     | Backspace.                      |
| `HT`  | 9       | 011   | 0x09 | `\t`     | `^I`     | Tabulação horizontal.           |
| `LF`  | 10      | 012   | 0x0A | `\n`     | `^J`     | Avançar linha (nova linha).     |
| `VT`  | 11      | 013   | 0x0B | `\v`     | `^K`     | Tabulação Vertical.             |
| `FF`  | 12      | 014   | 0x0C | `\f`     | `^L`     | Nova página.                    |
| `CR`  | 13      | 015   | 0x0D | `\r`     | `^M`     | Retornar ao começo da linha.    |
| `ESC` | 27      | 033   | 0x1B | `\e`[*](#escape) | `^[` | Caractere de escape (ESC).  |
| `DEL` | 127     | 177   | 0x7F | `<none>` | `<none>` | Apagar o caractere.             |
