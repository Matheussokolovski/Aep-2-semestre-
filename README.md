//# Aep-2-semestre-
//Cyber segurança replanejamento de sistemas de defesa empresarial

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <locale.h>

#define MAX_USUARIOS 100
#define ARQUIVO_USUARIOS "usuarios.txt" 

struct dados {
    char nome[50];
    char senha[50]; 
};


void criptografar_senha(const char *senha, char *hash) {
    int sum = 0;
    for (int i = 0; senha[i] != '\0'; i++) {
        sum += senha[i] * (i + 1); 
    }
    sprintf(hash, "%d", sum); 
}


void salvar_usuarios(struct dados *usuarios, int totalUsuarios) {
    FILE *arquivo = fopen(ARQUIVO_USUARIOS, "w");
    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo para salvar.\n");
        return;
    }
    for (int i = 0; i < totalUsuarios; i++) {
        fprintf(arquivo, "%s %s\n", usuarios[i].nome, usuarios[i].senha); 
    }
    fclose(arquivo);
}


int carregar_usuarios(struct dados *usuarios) {
    FILE *arquivo = fopen(ARQUIVO_USUARIOS, "r");
    if (arquivo == NULL) return 0;

    int totalUsuarios = 0;
    while (fscanf(arquivo, "%s %s", usuarios[totalUsuarios].nome, usuarios[totalUsuarios].senha) == 2) {
        totalUsuarios++;
    }
    fclose(arquivo);
    return totalUsuarios;
}

int main(void) {
    setlocale(LC_ALL, "portuguese");

    int escolha = 1, totalUsuarios = 0;
    struct dados usuarios[MAX_USUARIOS];

    
    totalUsuarios = carregar_usuarios(usuarios);

    while (escolha != 5) {
        printf("\n Seja bem-vindo(a).");
        printf("\n 1 - Login.");
        printf("\n 2 - Criar conta.");
        printf("\n 3 - Excluir conta.");
        printf("\n 4 - Listar usuários.");
        printf("\n 5 - Fechar Programa.");
        printf("\n\n Escolha uma opcao: ");
        scanf("%d", &escolha);
        fflush(stdin);

        switch (escolha) {
            case 1: { // Login
                char nome[50], senha[50], senhaCriptografada[50];
                int loginSucesso = 0;

                printf("Digite seu nome de usuário: ");
                scanf("%s", nome);
                printf("Digite sua senha: ");
                scanf("%s", senha);
                criptografar_senha(senha, senhaCriptografada);

                for (int i = 0; i < totalUsuarios; i++) {
                    if (strcmp(usuarios[i].nome, nome) == 0 && strcmp(usuarios[i].senha, senhaCriptografada) == 0) {
                        printf("Login bem-sucedido!\n");
                        loginSucesso = 1;
                        break;
                    }
                }
                if (!loginSucesso) printf("Nome de usuário ou senha incorretos.\n");
                break;
            }
            case 2: { // Criar conta
                if (totalUsuarios >= MAX_USUARIOS) {
                    printf("Limite de usuários atingido.\n");
                    break;
                }
                struct dados novoUsuario;
                char senha[50];

                printf("Digite um nome de usuário: ");
                scanf("%s", novoUsuario.nome);
                printf("Digite uma senha: ");
                scanf("%s", senha);
                criptografar_senha(senha, novoUsuario.senha);

                usuarios[totalUsuarios++] = novoUsuario;
                salvar_usuarios(usuarios, totalUsuarios);
                printf("Conta criada com sucesso!\n");
                break;
            }
            case 3: { 
                char nome[50];
                int encontrado = 0;

                printf("Digite o nome de usuário a ser excluído: ");
                scanf("%s", nome);

                for (int i = 0; i < totalUsuarios; i++) {
                    if (strcmp(usuarios[i].nome, nome) == 0) {
                        for (int j = i; j < totalUsuarios - 1; j++) {
                            usuarios[j] = usuarios[j + 1];
                        }
                        totalUsuarios--;
                        salvar_usuarios(usuarios, totalUsuarios);
                        printf("Conta excluída com sucesso!\n");
                        encontrado = 1;
                        break;
                    }
                }
                if (!encontrado) printf("Usuário não encontrado.\n");
                break;
            }
            case 4: { 
                printf("\nLista de usuários:\n");
                for (int i = 0; i < totalUsuarios; i++) {
                    printf("Usuário %d: %s\n", i + 1, usuarios[i].nome);
                }
                break;
            }
            case 5: { 
                printf("Encerrando o programa.\n");
                break;
            }
            default:
                printf("Opção inválida!\n");
        }
    }

    return 0;
}
