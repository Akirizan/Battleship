# Battleship
Welcome to Battleship, this is a recreation of the famous game Battleship, you fight against a computer and very much like the u place ur boats and then play the game

const val MENU_PRINCIPAL = 100
const val MENU_DEFINIR_TABULEIRO = 101
const val MENU_DEFINIR_NAVIOS = 102
const val MENU_JOGAR = 103
const val MENU_LER_FICHEIRO = 104
const val MENU_GRAVAR_FICHEIRO = 105
const val SAIR = 106
var numLinhas = -1
var numColunas = -1
var tabuleiroHumano: Array<Array<Char?>> = emptyArray()
var tabuleiroComputador: Array<Array<Char?>> = emptyArray()
var tabuleiroPalpitesDoHumano: Array<Array<Char?>> = emptyArray()
var tabuleiroPalpitesDoComputador: Array<Array<Char?>> = emptyArray()
fun menu(): Int {
     main()
    return 1
}
fun calculaNumNavios (numLinhas: Int,numColunas: Int): Array<Int> {
    if (tamanhoTabuleiroValido(numLinhas,numColunas)){
        return when(numLinhas){
            4 -> arrayOf(2,0,0,0)  //submarinos,contra-torpedos,navios-tanque,porta-avioes
            5 -> arrayOf(1,1,1,0)
            7 -> arrayOf(2,1,1,1)
            8 -> arrayOf(2,2,1,1)
            10 -> arrayOf(3,2,1,1)
            else -> arrayOf()
        }
    }
    return arrayOf()
}
fun criaTabuleiroVazio(numLinhas: Int,numColunas: Int): Array<Array<Char?>>{
    val arrayDeNulls = Array(numLinhas){ arrayOfNulls<Char?>(numColunas) }
    return arrayDeNulls
}
fun coordenadaContida(tabuleiro: Array<Array<Char?>>, linha: Int, coluna: Int): Boolean {
    if ((linha==1&&coluna==1)||(linha==1&&coluna==tabuleiro[0].size)||
        (coluna==1&&linha==tabuleiro.size)|| (linha==tabuleiro.size&&coluna==tabuleiro[0].size)){
return false
    }
    return linha in 1..tabuleiro.size && coluna in 1..tabuleiro[0].size
}
fun limparCoordenadasVazias(arrayPair: Array<Pair<Int, Int>>): Array<Pair<Int, Int>> {
    var countSemZeros = 0
    for (pair in arrayPair) {
        if (pair != Pair(0, 0)) {
            countSemZeros++
        }
    }
    val arraySemZeros = Array(countSemZeros) { Pair(0, 0) }
    var count = 0
    for (posicao in arrayPair) {
        if (posicao != Pair(0, 0)) {
            arraySemZeros[count++] = posicao
        }
    }
    return arraySemZeros
}
fun juntarCoordenadas(array1:Array<Pair<Int,Int>>,array2: Array<Pair<Int,Int>>): Array<Pair<Int,Int>>{
    return array1 + array2
}
fun gerarCoordenadasNavio(tabuleiro: Array<Array<Char?>>,linha:Int,coluna: Int,orientacao: String,dimensao:Int):Array<Pair<Int,Int>>{
    val arrayDePares = Array(dimensao){Pair(0,0)}
    if (coordenadaContida(tabuleiro,linha,coluna)) {
        when (orientacao) {
            "N" -> {
                for (posicao in 0..dimensao - 1) {
                    arrayDePares[posicao] = Pair(linha - posicao, coluna)
                    if (linha - posicao !in 1..tabuleiro.size) {
                        return arrayOf()
                    }
                }
            }

            "E" -> {
                for (posicao in 0..dimensao - 1) {
                    arrayDePares[posicao] = Pair(linha, coluna + posicao)
                    if (coluna + posicao !in 1..tabuleiro.size) {
                        return arrayOf()
                    }
                }
            }

            "O" -> {
                for (posicao in 0..dimensao - 1) {
                    arrayDePares[posicao] = Pair(linha, coluna - posicao)
                    if (coluna - posicao !in 1..tabuleiro.size) {
                        return arrayOf()
                    }
                }
            }

            "S" -> {
                for (posicao in 0..dimensao - 1) {
                    arrayDePares[posicao] = Pair(linha + posicao, coluna)
                    if (linha + posicao !in 1..tabuleiro.size) {
                        return arrayOf()
                    }
                }
            }
        }
    }
    return arrayDePares
}
fun gerarCoordenadasFronteira(tabuleiro: Array<Array<Char?>>, linha:Int, coluna: Int, orientacao: String, dimensao:Int): Array<Pair<Int,Int>>{
    val arrayDePares = gerarCoordenadasNavio(tabuleiro,linha,coluna,orientacao,dimensao)
    val arrayDeFronteira = Array(14){Pair(0,0)}
    var count = 0
    when(orientacao){
        "N"->{
            for (linhas in linha-dimensao..linha +1){
                for (colunas in coluna - 1.. coluna+1) {
                    if (coordenadaContida(tabuleiro,linhas,colunas)) {
                        if (Pair(linhas,colunas) !in arrayDePares) {
                            arrayDeFronteira[count]=Pair(linhas, colunas)
                        }
                        count++
                    }
                }
            }
        }
        "E"->{
            for (linhas in linha-1..linha +1){
                for (colunas in coluna-1.. coluna+dimensao) {
                    if (coordenadaContida(tabuleiro,linhas,colunas)) {
                        if (Pair(linhas,colunas) !in arrayDePares) {
                            arrayDeFronteira[count]=Pair(linhas, colunas)
                        }
                        count++
                    }
                }
            }
        }
        "O"->{
            for (linhas in linha-1..linha +1){
                for (colunas in coluna-dimensao.. coluna-1) {
                    if (coordenadaContida(tabuleiro,linhas,colunas)) {
                        if (Pair(linhas,colunas) !in arrayDePares) {
                            arrayDeFronteira[count]=Pair(linhas, colunas)
                        }
                        count++
                    }
                }
            }
        }
        "S"->{
            for (linhas in linha-1..linha +dimensao){
                for (colunas in coluna - 1.. coluna+1) {
                    if (coordenadaContida(tabuleiro,linhas,colunas)) {
                        if (Pair(linhas,colunas) !in arrayDePares) {
                            arrayDeFronteira[count]=Pair(linhas, colunas)
                        }
                        count++
                    }
                }
            }
        }
    }
    return limparCoordenadasVazias(arrayDeFronteira)
}
fun estaLivre(tabuleiro: Array<Array<Char?>>, array: Array<Pair<Int,Int>>): Boolean {
    for(coordenada in array) {
        val linha = coordenada.first
        val coluna = coordenada.second
        if (!coordenadaContida(tabuleiro,linha,coluna)){
            return false
        }
        if(tabuleiro[linha - 1][coluna - 1] != null){
            return false
        }
    }
    return true
}
fun insereNavioSimples(tabuleiro: Array<Array<Char?>>,linha:Int,coluna: Int,dimensao:Int):Boolean{
    val arrayDePares = gerarCoordenadasNavio(tabuleiro,linha,coluna,"E",dimensao)
    val char = dimensao.toString()
    val fronteira = gerarCoordenadasFronteira(tabuleiro,linha,coluna,"E",dimensao)
    val junta = juntarCoordenadas(arrayDePares,fronteira)
    if (arrayDePares.isEmpty()){
        return false
    }
    if(coordenadaContida(tabuleiro,linha, coluna)) {
        if (estaLivre(tabuleiro, junta)) {
            for (array in arrayDePares) {
                val linhas = array.first - 1
                val colunas = array.second - 1
                tabuleiro[linhas][colunas] = char[0]
            }
            return true
        }
    }
    return false
}
fun insereNavio(tabuleiro: Array<Array<Char?>>,linha:Int,coluna: Int,orientacao: String,dimensao:Int):Boolean{
    val arrayDePares = gerarCoordenadasNavio(tabuleiro,linha,coluna,orientacao,dimensao)
    val char = dimensao.toString()
    val fronteira = gerarCoordenadasFronteira(tabuleiro,linha,coluna,orientacao,dimensao)
    val junta = juntarCoordenadas(arrayDePares,fronteira)
    if (arrayDePares.isEmpty()){
        return false
    }
    if(coordenadaContida(tabuleiro,linha, coluna)) {
        if (estaLivre(tabuleiro, junta)) {
            for (array in arrayDePares) {
                val linhas = array.first - 1
                val colunas = array.second - 1
                tabuleiro[linhas][colunas] = char[0]
            }
            return true
        }
    }
    return false
}
fun preencheTabuleiroComputador(tabuleiro: Array<Array<Char?>>,array1: Array<Int>){
for (posicao in 0..array1.size-1){
    var resto = array1[posicao]
    val arrayOrientacao = arrayOf("N","E","O","S")
    while (resto>0){
        val linha = (0.. tabuleiro.size-1).random()
        val coluna = (0.. tabuleiro.size-1).random()
        val orientacao = arrayOrientacao.random()
        if(insereNavio(tabuleiro,linha+1,coluna+1,orientacao,posicao+1)){
            resto--
        }
    }
}
}
fun navioCompleto(tabuleiro: Array<Array<Char?>>, linha: Int, coluna: Int): Boolean {
    val navioAtual = tabuleiro[linha - 1][coluna - 1]

    fun verificaNavioNaDirecao(direcaoLinha: Int, direcaoColuna: Int): Boolean {
        var linhaAtual = linha
        var colunaAtual = coluna
        var tamanhoNavio = '0'

        while (coordenadaContida(tabuleiro, linhaAtual, colunaAtual) &&
            tabuleiro[linhaAtual - 1][colunaAtual - 1] == navioAtual) {
            tamanhoNavio++
            linhaAtual += direcaoLinha
            colunaAtual += direcaoColuna
        }

        linhaAtual = linha - direcaoLinha
        colunaAtual = coluna - direcaoColuna

        while (coordenadaContida(tabuleiro, linhaAtual, colunaAtual) &&
            tabuleiro[linhaAtual - 1][colunaAtual - 1] == navioAtual) {
            tamanhoNavio++
            linhaAtual -= direcaoLinha
            colunaAtual -= direcaoColuna
        }
        return tamanhoNavio == (navioAtual)
    }
    return (coordenadaContida(tabuleiro, linha, coluna) && (verificaNavioNaDirecao(0, 1)||
            verificaNavioNaDirecao(0, -1)||
            verificaNavioNaDirecao(1, 0) || verificaNavioNaDirecao(-1, 0)))
}
fun obtemMapa(tabuleiroPalpites: Array<Array<Char?>>,valor:Boolean): Array<String> {
val linha = tabuleiroPalpites.size
val coluna = tabuleiroPalpites[0].size
val mapa = Array(linha+1){ "" }
mapa[0]="| ${criaLegendaHorizontal(coluna)} |"
    for (posicao in 1..linha){
        for (position in 1..coluna){
            val navio = tabuleiroPalpites[posicao-1][position-1]
            if (navio==null){
                mapa[posicao]+= if (valor){
                    "| ~ "
                }else{
                    "| ? "
                }
                } else if (!valor){
                    val num = when{
                        navio=='2'&& !navioCompleto(tabuleiroPalpites,linha,coluna) -> '\u2082'
                        navio=='3'&& !navioCompleto(tabuleiroPalpites,linha,coluna) -> '\u2083'
                        navio=='4'&& !navioCompleto(tabuleiroPalpites,linha,coluna) -> '\u2084'
                        else-> navio
                    }
                mapa[posicao]+="| $num "
            }else{
                mapa[posicao]+="| $navio "
            }
        }
        mapa[posicao]+= "| $posicao"
    }
    return mapa
}
fun lancarTiro(tabuleiroReal: Array<Array<Char?>>,tabuleiroPalpites: Array<Array<Char?>>,arrayLinhasColunas: Pair<Int,Int>):String{
val linha = arrayLinhasColunas.first -1
val coluna = arrayLinhasColunas.second -1
tabuleiroPalpites[linha][coluna] = when(tabuleiroReal[linha][coluna]){
    null-> 'X'
    '1' -> '1'
    '2' -> '2'
    '3' -> '3'
    else -> '4'
}
return when (tabuleiroPalpites[linha][coluna]){
    'X' -> "Agua."
    '1' -> "Tiro num submarino."
    '2' -> "Tiro num contra-torpedeiro."
    '3' -> "Tiro num navio-tanque."
    else -> "Tiro num porta-avioes."
}
}
fun geraTiroComputador(tabuleiroPalpitesComputador: Array<Array<Char?>>): Pair<Int,Int>{
val linha = 0 .. tabuleiroPalpitesComputador.size -1
val coluna = 0 .. tabuleiroPalpitesComputador[0].size -1
var linhasComputador = linha.random()
var colunasComputador = coluna.random()
while (tabuleiroPalpitesComputador[linhasComputador][colunasComputador]!=null){
    linhasComputador = linha.random()
    colunasComputador = coluna.random()
}
    return Pair(linhasComputador+1,colunasComputador+1)
}
fun contarNaviosDeDimensao(tabuleiroReal: Array<Array<Char?>>, dimensao: Int): Int {
    var count = 0

    for (linha in 0 until tabuleiroReal.size) {
        for (coluna in 0 until tabuleiroReal[linha].size) {
            when (dimensao) {
                1 -> if (tabuleiroReal[linha][coluna] == '1') count++
                2 -> {
                    val colunas = coluna < tabuleiroReal[linha].size - 1 &&
                            tabuleiroReal[linha][coluna] == '2' &&
                            tabuleiroReal[linha][coluna + 1] == '2'
                    val linhas = linha < tabuleiroReal.size - 1 &&
                            tabuleiroReal[linha][coluna] == '2' &&
                            tabuleiroReal[linha + 1][coluna] == '2'
                    if (colunas || linhas) count++
                }
                3 -> {
                    val colunas = coluna in 1 until tabuleiroReal[linha].size - 1 &&
                            tabuleiroReal[linha][coluna] == '3' &&
                            tabuleiroReal[linha][coluna - 1] == '3' &&
                            tabuleiroReal[linha][coluna + 1] == '3'
                    val linhas = linha in 1 until tabuleiroReal.size - 1 &&
                            tabuleiroReal[linha][coluna] == '3' &&
                            tabuleiroReal[linha - 1][coluna] == '3' &&
                            tabuleiroReal[linha + 1][coluna] == '3'
                    if (colunas || linhas) count++
                }
                4 -> {
                    val colunas = coluna in 1 until tabuleiroReal[linha].size - 2 &&
                            tabuleiroReal[linha][coluna] == '4' &&
                            tabuleiroReal[linha][coluna - 1] == '4' &&
                            tabuleiroReal[linha][coluna + 1] == '4' &&
                            tabuleiroReal[linha][coluna + 2] == '4'
                    val linhas = linha in 1 until tabuleiroReal.size - 2 &&
                            tabuleiroReal[linha][coluna] == '4' &&
                            tabuleiroReal[linha - 1][coluna] == '4' &&
                            tabuleiroReal[linha + 1][coluna] == '4' &&
                            tabuleiroReal[linha + 2][coluna] == '4'
                    if (colunas || linhas) count++
                }
            }
        }
    }
    return count
}
fun venceu(tabuleiroPalpitesHumano: Array<Array<Char?>>):Boolean{
    val numNavios= calculaNumNavios(tabuleiroPalpitesHumano.size,tabuleiroPalpitesHumano[0].size)
for (dimensao in 1..numNavios.size){
    val condicaoParaVencer = numNavios[dimensao-1]-1
    val condicaoAtual = contarNaviosDeDimensao(tabuleiroPalpitesHumano,dimensao)
    if ((condicaoParaVencer)!=condicaoAtual){
        return false
    }
}
    return true
}
fun lerJogo(nomeDeFicheiro:String,tipoDeTabuleiro:Int): Array<Array<Char?>>{
return arrayOf(arrayOf())
}
fun gravarJogo(nomeDeFicheiro: String,tabuleiroRealHumano: Array<Array<Char?>>,
               tabuleiroPalpitesHumano: Array<Array<Char?>>,
               tabuleiroRealComputador: Array<Array<Char?>>,
               tabuleiroPalpitesComputador: Array<Array<Char?>>){
return
}
fun tamanhoTabuleiroValido (numLinha: Int?,numColuna: Int?): Boolean {
        return when{
            numLinha==4 && numColuna==4 -> true
            numLinha==5 && numColuna==5 -> true
            numLinha==7 && numColuna==7 -> true
            numLinha==8 && numColuna==8 -> true
            numLinha==10 && numColuna==10 -> true
            else -> false
        }

    }
fun processaCoordenadas(coordenada:String,numLinhas:Int,numColuna: Int):Pair<Int,Int>?{
    if (coordenada.length !in 2..5) {
        return null
    }
    if (coordenada.length==3){
        if (coordenada[2].toInt() in 64..91) {
            if (numLinhas + 49 > coordenada[0].toInt() && numColuna + 65 > coordenada[2].toInt()){
                return Pair(coordenada[0].toInt()-48,coordenada[2].toInt()-64)
            }
        }else{
            return null
        }
    }else{
        if (coordenada[3].toInt() in 64..91) {
            if (numLinhas + 96 > (coordenada[0].toInt() + coordenada[1].toInt()) && numColuna + 64 > coordenada[3].toInt()){
                return Pair(coordenada[0].toInt()+coordenada[1].toInt()-87 ,coordenada[3].toInt()-64)
            }
        }else{
            return null
        }
    }
    return null
}
fun criaLegendaHorizontal(coluna:Int): String{
    var countLegenda = 1
    val abc = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    var sequencia = "A"
    do{
        if (countLegenda==1 && coluna!=1) {
            sequencia+= " |"
            sequencia += if (countLegenda != coluna - 1) {
                " " + abc[countLegenda] + " |"
            } else {
                " " + abc[countLegenda]
            }
        } else if(countLegenda!=1){
            sequencia += if (countLegenda != coluna - 1) {
                " " + abc[countLegenda] + " |"
            } else {
                " " + abc[countLegenda]
            }
        }
        countLegenda++
    }while (countLegenda<coluna)
    return sequencia
}
fun criaTerreno(linha:Int,coluna: Int): String {
    var count =1
    var sequencia = "\n| ${criaLegendaHorizontal(coluna)} |\n"
    while (count<=linha){
        var countEspaco = 0
        while (countEspaco<coluna){
            sequencia += "|   "
            countEspaco++
        }
        sequencia+= "| $count\n"
        count++
    }
    return sequencia

}
fun menuPrincipal(): Any {
    println(    "\n" +
            "> > Batalha Naval < <\n" +
            "\n" +
            "1 - Definir Tabuleiro e Navios\n" +
            "2 - Jogar\n" +
            "3 - Gravar\n" +
            "4 - Ler\n" +
            "0 - Sair\n"
    )
    val todosNum = arrayOf(-1,0,1,2,3,4,9)
    val arrayNum = arrayOf(2,3,4)
    var numero = readln().toIntOrNull()
    for (posicao in 0..2) {
        if (numero == arrayNum[posicao] && tabuleiroHumano.isEmpty() && tabuleiroComputador.isEmpty()
            && tabuleiroPalpitesDoComputador.isEmpty() && tabuleiroPalpitesDoHumano.isEmpty()){
            println("!!! Tem que primeiro definir o tabuleiro do jogo, tente novamente")
            return menu()
        }
    }
    while (numero !in todosNum) {
        println("!!! Opcao invalida, tente novamente")
        numero = readln().toIntOrNull()
    }
val array09 = arrayOf(0,9)
    return when (numero) {
        in array09-> {
            println("Vou sair")
            SAIR}
        1 -> MENU_DEFINIR_TABULEIRO
        2 -> MENU_JOGAR
        3 -> MENU_GRAVAR_FICHEIRO
        4 -> MENU_LER_FICHEIRO
        else -> MENU_PRINCIPAL

    }
}
fun menuDefinirTabuleiro(): Int {
    println(
        "\n> > Batalha Naval < <\n" +
                "\n" +
                "Defina o tamanho do tabuleiro:\n" +
                "Quantas linhas?"
    )
    var linhas = readln().toIntOrNull()
    if (linhas==-1){
        return menu()
    }
    if (linhas==0){
        return SAIR
    }
    if (linhas != null) {
        numLinhas = linhas
    }
    while (linhas == null || linhas < 0||linhas>26||!tamanhoTabuleiroValido(linhas,linhas)) {
        println("!!! Número de linhas invalidas, tente novamente\n" +
                "Quantas linhas?")
        linhas = readln().toIntOrNull()
        if (linhas==-1){
            return menu()
        }
        if (linhas==0){
            return SAIR
        }
    }
    println("Quantas colunas?")
    var colunas = readln().toIntOrNull()
    if (colunas==-1){
        return menu()
    }
    if (colunas==0){
        return SAIR
    }
    if (colunas != null) {
        numColunas = colunas
    }
    while (colunas == null || colunas < 0 ||colunas>26 || !tamanhoTabuleiroValido(linhas, colunas)) {
        println("!!! Número de colunas invalidas, tente novamente\n" +
                "Quantas colunas?")
        colunas = readln().toIntOrNull()
        if (colunas==-1){
            return menu()
        }
        if (colunas==0){
           return SAIR
        }
    }
    tabuleiroHumano = criaTabuleiroVazio(linhas,colunas)
    tabuleiroComputador = criaTabuleiroVazio(linhas,colunas)
    tabuleiroPalpitesDoComputador = criaTabuleiroVazio(linhas,colunas)
    tabuleiroPalpitesDoHumano = criaTabuleiroVazio(linhas,colunas)
    val numNavios = calculaNumNavios(linhas,colunas)
    preencheTabuleiroComputador(tabuleiroComputador,numNavios)
    for (mapa in obtemMapa(tabuleiroHumano,true)) {
        println(mapa)
    }
    return MENU_DEFINIR_NAVIOS
}
fun menuDefinirNavios(): Int {
    val numNavios = calculaNumNavios(numLinhas,numColunas)
    var nomeNavios = ""
    for (position in 1..numNavios.size) {
        var count =numNavios[position-1]
        while(count>0) {
            when(position) {
                1 -> nomeNavios = "submarino"
                2 -> nomeNavios = "contra-torpedeiro"
                3 -> nomeNavios = "navios-tanque"
                4 -> nomeNavios = "porta-avioes"
            }
            println("Insira as coordenadas de um $nomeNavios:\nCoordenadas? (ex: 6,G)"
            )
            var coordenadas = readln()
            if (coordenadas == "-1") return menu()
            if (coordenadas == "0") return SAIR
            val parCoordenadas = processaCoordenadas(coordenadas, numLinhas, numColunas)
            while (parCoordenadas == null) {
                println("!!! Coordenadas invalidas, tente novamente\nCoordenadas? (ex: 6,G)")
                coordenadas = readln()
                if (coordenadas == "-1") return menu()
                if (coordenadas == "0") return SAIR
            }
            var orientacao = "E"
            if (numLinhas > 4) {
                println("Insira a orientacao do navio:\nOrientacao? (N, S, E, O)"
                )
                orientacao = readln()
                if (orientacao == "-1") return menu()
                if (orientacao == "0") return SAIR
                while (orientacao != "N" && orientacao != "S" && orientacao != "E" && orientacao != "O") {
                    println(
                        "!!! Orientacao invalida, tente novamente\n" +
                                "Orientacao? (N, S, E, O)"
                    )
                    orientacao = readln()
                    if (orientacao == "-1") return menu()
                    if (orientacao == "0") return SAIR
                }
            }
            if (insereNavio(tabuleiroHumano, parCoordenadas.first, parCoordenadas.second, orientacao,position)) {
                count--
                for (mapa in obtemMapa(tabuleiroHumano, true)) {
                    println(mapa)
                }
            }else{
                println("!!! Posicionamento invalido, tente novamente.")
            }
        }
    }
    println("Pretende ver o mapa gerado para o Computador? (S/N)")
    val simNao = readLine() ?: ""
    if (simNao == "-1") return MENU_PRINCIPAL
    if (simNao == "S") { for (mapa in obtemMapa(tabuleiroComputador, true)) println(mapa)}
    return MENU_PRINCIPAL
}
fun menuJogar(): Int {
    do{
        val navioFundo = " Navio ao fundo!"
        for (mapa in obtemMapa(tabuleiroPalpitesDoHumano,false)){
            println(mapa)
        }
        println(
            "Indique a posição que pretende atingir\n" +
                    "Coordenadas? (ex: 6,G)"
        )
        val coordenadas = readln()
        if (coordenadas=="-1") {
            return menu()
        }
        if (coordenadas=="0") {
            return SAIR
        }
        val parCoordenadas = processaCoordenadas(coordenadas, numLinhas, numColunas)
        val tiroPC = geraTiroComputador(tabuleiroHumano)
        val tiroHumano = lancarTiro(tabuleiroComputador, tabuleiroPalpitesDoHumano, parCoordenadas!!)
        val textoHumano = ">>> HUMANO >>>" +
                "$tiroHumano${if (navioCompleto(tabuleiroPalpitesDoHumano, parCoordenadas.first, parCoordenadas.second)){ 
                    " Navio ao fundo!" +
                        "" }else ""}"
        val arrayString = arrayOf("Agua.", "Tiro num submarino.", "Tiro num contra-torpedeiro.", "Tiro num navio-tanque.", "Tiro num porta-avioes.")

        when (tiroHumano) {
            arrayString[0],arrayString[1],arrayString[2],arrayString[3],arrayString[4] -> println(textoHumano)
        }

        if (!venceu(tabuleiroPalpitesDoHumano)) {
            val textoTiroPC = lancarTiro(tabuleiroHumano, tabuleiroPalpitesDoComputador, tiroPC)
            val textoComputador = ">>> COMPUTADOR >>>$textoTiroPC"

            when (textoTiroPC) {
                arrayString[0],arrayString[1],arrayString[2],arrayString[3],arrayString[4] ->
                    println("Computador lancou tiro para a posicao $tiroPC\n$textoComputador\nPrima enter para continuar")
            }

            readln().toIntOrNull()
        }
    } while (!venceu(tabuleiroPalpitesDoComputador) && !venceu(tabuleiroPalpitesDoHumano))

    println("PARABENS! Venceu o jogo!\nPrima enter para voltar ao menu principal")
    readln().toIntOrNull()
    return MENU_PRINCIPAL
}
fun menuLerFicheiro(): Int {
    return MENU_PRINCIPAL
}
fun menuGravarFicheiro(): Int {
    return MENU_PRINCIPAL
}
fun main() {
    var menuActual = MENU_PRINCIPAL
    while (true){
        menuActual = when (menuActual){
            MENU_PRINCIPAL -> menuPrincipal() as Int
            MENU_DEFINIR_TABULEIRO -> menuDefinirTabuleiro()
            MENU_DEFINIR_NAVIOS -> menuDefinirNavios()
            MENU_JOGAR -> menuJogar()
            MENU_LER_FICHEIRO -> menuLerFicheiro()
            MENU_GRAVAR_FICHEIRO -> menuGravarFicheiro()
            SAIR -> return
            else -> return
        }
    }
}
