[github]: https://github.com/eduardobaginskicosta
[facebook]: https://facebook.com/eduardobaginskicosta/
[twitter]: https://twitter.com/eduardobcosta7/
[instagram]: https://instagram.com/eduardobcosta7/
[medium]: https://eduardobcosta.medium.com/
[youtube]: https://youtube.com/@eduardobcosta/
[linkedin]: https://www.linkedin.com/in/eduardobaginskicosta/
[ansi]: https://pt.wikipedia.org/wiki/American_National_Standards_Institute

# ANSI Escape Codes

Olá caros programadores, eu sou [Eduardo Baginski Costa](github) um autodidata e um programador brasileiro.   
Através desse repositório irei apresentar exemplos de utilização do **ANSI Escape Codes (Códigos de escape [ANSI](ansi))** para formatar
saídas e entradas de texto em terminais. Os códigos **[ANSI](ansi)** podem não ser suportados de maneira nativa por algumas linguagens de
programação, como o **CSharp** sendo necessário a implementação de métodos da biblioteca **Kernel32**.

<h3 align="center">—&emsp;⚠️&emsp;Avisos Importantes&emsp;⚠️&emsp;—</h3>

Como mencionado anteriormente os códigos de escape **[ANSI](ansi)** podem não ser suportados nativamente por algumas linguagens de programação,
tal como o **CSharp**. Por esses e outros motivos recomendo que uma busca rápida seja realizada de como habilitar o suporte a esses códigos na
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

- ```0x0004```: quando oredenamos ao terminal para que exibida uma sequência de caracteres, antes deles serem exibidos ao usuário eles serão analisados para **VT100** e **sequências de caracteres de controle**, ou seja, que podem movimentar o mouse, alterar as colorações, as fontes além de outras operações executadas através da API do terminal.

- ```0x0008```: seu uso é destinado a utilização conjunta ao ```0x0004``` para melhorar a emulação do terminal, informando que quando um caractere é escrito no final da tela (canto inferior direito) a rolagem imediata deve ser desabilitada, sendo este o comportamento desejado.
