#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define TAM 5  // Tamanho do tabuleiro
#define NAVIOS 3  // Quantidade de navios por jogador

typedef struct {
    char grade[TAM][TAM];
} Tabuleiro;

void inicializarTabuleiro(Tabuleiro* t) {
    for (int i = 0; i < TAM; i++)
        for (int j = 0; j < TAM; j++)
            t->grade[i][j] = '~';  // Água
}

void mostrarTabuleiro(Tabuleiro* t, int ocultarNavios) {
    printf("  ");
    for (int j = 0; j < TAM; j++) printf(" %d", j);
    printf("\n");
    for (int i = 0; i < TAM; i++) {
        printf("%d ", i);
        for (int j = 0; j < TAM; j++) {
            if (ocultarNavios && t->grade[i][j] == 'N')
                printf(" ~");
            else
                printf(" %c", t->grade[i][j]);
        }
        printf("\n");
    }
}

void posicionarNavios(Tabuleiro* t, int jogador) {
    int linha, coluna;
    printf("Jogador %d, posicione seus %d navios.\n", jogador, NAVIOS);
    for (int i = 0; i < NAVIOS; i++) {
        mostrarTabuleiro(t, 0);
        printf("Navio %d - Linha e Coluna (0 a %d): ", i + 1, TAM - 1);
        scanf("%d %d", &linha, &coluna);
        if (linha < 0 || linha >= TAM || coluna < 0 || coluna >= TAM || t->grade[linha][coluna] == 'N') {
            printf("Posição inválida ou ocupada. Tente novamente.\n");
            i--;
        } else {
            t->grade[linha][coluna] = 'N';
        }
    }
    system("clear||cls");
}

int atirar(Tabuleiro* t, int linha, int coluna) {
    if (linha < 0 || linha >= TAM || coluna < 0 || coluna >= TAM)
        return -1;  // Posição inválida
    if (t->grade[linha][coluna] == 'N') {
        t->grade[linha][coluna] = 'X';  // Acertou
        return 1;
    } else if (t->grade[linha][coluna] == '~') {
        t->grade[linha][coluna] = 'O';  // Água
        return 0;
    } else {
        return -1;  // Já atirado
    }
}

int naviosRestantes(Tabuleiro* t) {
    int count = 0;
    for (int i = 0; i < TAM; i++)
        for (int j = 0; j < TAM; j++)
            if (t->grade[i][j] == 'N')
                count++;
    return count;
}

int main() {
    Tabuleiro jogador1, jogador2;
    int linha, coluna;
    int turno = 1;
    int resultado;

    inicializarTabuleiro(&jogador1);
    inicializarTabuleiro(&jogador2);

    // Posicionamento dos navios
    posicionarNavios(&jogador1, 1);
    posicionarNavios(&jogador2, 2);

    // Jogo
    while (1) {
        printf("Jogador %d, sua vez de atirar!\n", turno);
        if (turno == 1)
            mostrarTabuleiro(&jogador2, 1);
        else
            mostrarTabuleiro(&jogador1, 1);

        printf("Escolha linha e coluna (0 a %d): ", TAM - 1);
        scanf("%d %d", &linha, &coluna);

        if (turno == 1)
            resultado = atirar(&jogador2, linha, coluna);
        else
            resultado = atirar(&jogador1, linha, coluna);

        if (resultado == 1)
            printf("Acertou!\n");
        else if (resultado == 0)
            printf("Água!\n");
        else
            printf("Tiro inválido. Jogue novamente.\n");

        // Verificar vitória
        if (naviosRestantes(&jogador1) == 0) {
            printf("Jogador 2 venceu!\n");
            break;
        } else if (naviosRestantes(&jogador2) == 0) {
            printf("Jogador 1 venceu!\n");
            break;
        }

        if (resultado != -1)
            turno = (turno == 1) ? 2 : 1;

        printf("\nPressione ENTER para continuar...\n");
        getchar(); getchar();
        system("clear||cls");
    }

    return 0;
}
