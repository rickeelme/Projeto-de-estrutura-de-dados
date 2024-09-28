# Projeto-de-estrutura-de-dados

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Estrutura do nó para  cada visitante
typedef struct Visitante {
    char* nome;
    struct Visitante* proximo;
} Visitante;

// Função para criar um novo visitante (nó)
Visitante* criarVisitante(const char* nome) {
    Visitante* novo = (Visitante*)malloc(sizeof(Visitante));
    if (novo == NULL) {
        printf("Erro ao alocar memória!\n");
        exit(1);
    }
    novo->nome = (char*)malloc(strlen(nome) + 1);
    if (novo->nome == NULL) {
        printf("Erro ao alocar memória!\n");
        exit(1);
    }
    strcpy(novo->nome, nome);
    novo->proximo = novo;  // O próximo é ele mesmo na lista circular
    return novo;
}

// Função para adicionar um novo visitante à lista circular
void adicionarVisitante(Visitante** lista, const char* nome) {
    Visitante* novo = criarVisitante(nome);
    if (*lista == NULL) {
        *lista = novo;
    } else {
        Visitante* temp = *lista;
        while (temp->proximo != *lista) {
            temp = temp->proximo;
        }
        temp->proximo = novo;
        novo->proximo = *lista;
    }
    printf("Visitante %s adicionado.\n", nome);
}

// Função para buscar um visitante pelo nome
Visitante* buscarVisitante(Visitante* lista, const char* nome) {
    if (lista == NULL) return NULL;

    Visitante* temp = lista;
    do {
        if (strcmp(temp->nome, nome) == 0) {
            return temp;
        }
        temp = temp->proximo;
    } while (temp != lista);

    return NULL;
}

// Função para remover um visitante da lista
void removerVisitante(Visitante** lista, const char* nome) {
    if (*lista == NULL) return;

    Visitante* atual = *lista;
    Visitante* anterior = NULL;

    // Se a lista tem apenas um nó
    if (atual->proximo == *lista && strcmp(atual->nome, nome) == 0) {
        free(atual->nome);
        free(atual);
        *lista = NULL;
        printf("Visitante %s removido.\n", nome);
        return;
    }

    do {
        if (strcmp(atual->nome, nome) == 0) {
            if (anterior != NULL) {
                anterior->proximo = atual->proximo;
            } else {
                // Remover o primeiro nó
                Visitante* temp = *lista;
                while (temp->proximo != *lista) {
                    temp = temp->proximo;
                }
                temp->proximo = atual->proximo;
                *lista = atual->proximo;
            }
            free(atual->nome);
            free(atual);
            printf("Visitante %s removido.\n", nome);
            return;
        }
        anterior = atual;
        atual = atual->proximo;
    } while (atual != *lista);

    printf("Visitante %s não encontrado.\n", nome);
}

// Função para contar o número de visitantes
int contarVisitantes(Visitante* lista) {
    if (lista == NULL) return 0;

    int contagem = 0;
    Visitante* temp = lista;
    do {
        contagem++;
        temp = temp->proximo;
    } while (temp != lista);

    return contagem;
}

// Função para liberar toda a lista e a memória alocada
void liberarLista(Visitante** lista) {
    if (*lista == NULL) return;

    Visitante* atual = *lista;
    Visitante* temp;
    do {
        temp = atual->proximo;
        free(atual->nome);
        free(atual);
        atual = temp;
    } while (atual != *lista);

    *lista = NULL;
}

int main() {
    Visitante* lista = NULL;
    char nome[100];
    int escolha;

    while (1) {
        printf("\n--- Menu de Visitantes ---\n");
        printf("\n");
        printf("1. Adicionar visitante\n");
        printf("2. Remover visitante\n");
        printf("3. Buscar visitante\n");
        printf("4. Contar visitantes\n");
        printf("5. Sair\n");
        printf("\n");
        printf("Digite uma opção de 1 a 5: ");
        scanf("%d", &escolha);

        switch (escolha) {
            case 1:
                printf("Digite o nome do visitante a adicionar: ");
                scanf("%s", nome);
                adicionarVisitante(&lista, nome);
                break;
            case 2:
                printf("Digite o nome do visitante a remover: ");
                scanf("%s", nome);
                removerVisitante(&lista, nome);
                break;
            case 3:
                printf("Digite o nome do visitante a buscar: ");
                scanf("%s", nome);
                Visitante* encontrado = buscarVisitante(lista, nome);
                if (encontrado != NULL) {
                    printf("Visitante %s encontrado.\n", encontrado->nome);
                } else {
                    printf("Visitante %s não encontrado.\n", nome);
                }
                break;
            case 4:
                printf("Número de visitantes: %d\n", contarVisitantes(lista));
                break;
            case 5:
                liberarLista(&lista);
                printf("Saindo...\n");
                return 0;
            default:
                printf("Opção inválida! Tente novamente.\n");
        }
    }
}
