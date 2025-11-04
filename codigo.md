.data
arquivo:        .asciiz "C:/Users/kacio/OneDrive/Documentos/exercicio/lista.txt"
.align 2
aquiEstaMeusNumeros:          .space 1024        # aqui vai o conteúdo lido do arquivo
.align 2
espacoParaNumeros:        .space 400         # até 100 números inteiros
espaco:         .asciiz " "        # espaço para imprimir

.text
main:
# ---------- Abrir arquivo ----------
    li $v0, 13
    la $a0, arquivo        # nome do arquivo
    li $a1, 0              # modo leitura
    syscall
    move $s0, $v0          # guarda o numero que o arquivo ganhou pra usar em funções como fechar

# ---------- ler arquivo ----------
    move $a0, $s0
    la $a1, aquiEstaMeusNumeros          # onde vai armazenar o conteúdo lido
    li $a2, 1024           #espaço que eu reservei para ele 
    li $v0, 14		   #comando para ler meu arquivo
    syscall

# ---------- fechar arquivo ----------
    move $a0, $s0    #passa aquele arquivo aberto para saber qual ele vai fechar
    li $v0, 16
    syscall


# ---------- converter texto em números ----------
    la $t0, aquiEstaMeusNumeros
    la $t1, espacoParaNumeros
    li $t2, 0              # acumulador do número
    li $t3, 0              # para negativo: quando tem numero negativo ele le "-2", sendo "-"
    li $t7, 0              # contador de números

ler_loop:
    lb $t4, 0($t0)
    beqz $t4, fim_converter  #SE T4 IGUAL a zero acabou o programa

    li $t5, 44             # vírgula ','
    beq $t4, $t5, guardar  #é um numero positivo, guarda ele

    li $t5, 45             # sinal '-' valores da tabela ASCIIZ
    beq $t4, $t5, marcar_negativo    #é um numero negativo, vai ter que fazer um processo com ele 

    li $t5, 48
    sub $t4, $t4, $t5
    mul $t2, $t2, 10
    add $t2, $t2, $t4
    j proximo


proximo:
    addi $t0, $t0, 1  #incrementa 
    j ler_loop

marcar_negativo:
    li $t3, 1
    j proximo

guardar:
    beq $t3, 1, negativo
salvar:
    sw $t2, 0($t1)
    addi $t1, $t1, 4
    li $t2, 0
    li $t3, 0
    addi $t7, $t7, 1
    j proximo

negativo:
    sub $t2, $zero, $t2
    j salvar



fim_converter:


# ---------- hora de ordenar usnado o codigo do professor (Bubble Sort) ----------
    move $t8, $t7

inicio_ordenar:
    li $t2, 0              # marca se trocou algo
    li $t3, 0              # contador interno
    la $t0, espacoParaNumeros

loop_ordenar:
    lw $t4, 0($t0)
    lw $t5, 4($t0)
    ble $t4, $t5, nao_troca

    # troca
    sw $t5, 0($t0)
    sw $t4, 4($t0)
    li $t2, 1

nao_troca:
    addi $t0, $t0, 4
    addi $t3, $t3, 1
    addi $t6, $t8, -1
    blt $t3, $t6, loop_ordenar

    beqz $t2, pronto
    j inicio_ordenar

pronto:


# ---------- Imprimir números ----------
    la $t0, espacoParaNumeros
    li $t3, 0

imprimir:
    beq $t3, $t8, fim
    lw $a0, 0($t0)
    li $v0, 1
    syscall

    li $v0, 4
    la $a0, espaco
    syscall

    addi $t0, $t0, 4
    addi $t3, $t3, 1
    j imprimir

fim:
    li $v0, 10
    syscall
    
    
