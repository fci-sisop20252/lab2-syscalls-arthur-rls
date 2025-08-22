# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls (porém só ocorre realmente 1 write() mesmo no arquivo)
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
Essa diferença ocorre pela utilização de printf() e write(), printf reduz o número de syscall, fazendo apenas uma ao final
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
O método mais previsível é o write(), porque cada chamada gera exatamente uma syscall correspondente no kernel.
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
O file descriptor usado foi o 3, pois os file descriptor 0, 1 e 2 sâo utilizados pelo sistema para stdin, stdout e stdeer.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Pois o retorno do read indica quantos bytes foram lidos, 127.
```

**3. Por que verificar retorno de cada syscall?**

```
Pois as operações podem sempre falhar por diversos motivos, então é uma forma de garantir a funcionalidade.
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000115 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |87|0.000192|
| 64          |21|0.000115|
| 256         |6|0.000069|
| 1024        |2|0.000064|

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto menor o buffer, são realizadas mais syscalls.
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Não, a útlima chamada geralmente lê menos, pois lê apenas o "resto" do final do arquivo.
```

**3. Qual é a relação entre syscalls e performance?**

```
A quantidade de syscalls afeta diretamente a performance, pois o custo de syscall é caro, já que envolve troca com o kernell, então buffers maiores, que possuem menos syscalls, tem melhor performance.
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000208 segundos
- Throughput: 6404.00 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [x] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Para garantir que os dados da origem foram copiados para os lidos, dessa forma garante que a quantidade de bytes é igual, que já é um grande indicativo que foi concluída com sucesso.
```

**2. Que flags são essenciais no open() do destino?**

```
O_WRONLY - write only, só escrita, O_CREATE - cria o arquivo e O_TRUNC - zera o arquivo caso já exista.
```

**3. O número de reads e writes é igual? Por quê?**

```
Sim, pois a mesma leitura da origem deve ser escrita no destino.
```

**4. Como você saberia se o disco ficou cheio?**

```
O write possui uma verificação para isso: retornar -1;
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
O file description iria continuar aberto e consumindo os recursos do sistema, além que podem gerar erros para escrita e dados que só são completamente gravados ao fim do arquivo.
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
A syscall é o contato entre o usuário e o kernel, fazendo as operações de baixo nível, como read, que o usuário não poderia fazer sem o kernel.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
File descriptors são identificadores que permitem ao kernel entender a utilidade da operação e gerenciar arquivos e dados I/O.
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quanto maior o buffer, são realizados menos syscalls, que utilizam menos o kernel e a performance é melhor.
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** _____

**Por que você acha que foi mais rápido?**

```
Cp do sistema, pois é otimizado para operações de I/O diretamente ao kernel.
```

---

## 📤 Entrega
Certifique-se de ter:
- [X] Todos os códigos com TODOs completados
- [X] Traces salvos em `traces/`
- [X] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
