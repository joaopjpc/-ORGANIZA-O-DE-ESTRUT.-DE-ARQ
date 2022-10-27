# -ORGANIZA-O-DE-ESTRUT.-DE-ARQ

#include <stdio.h>
#include <string.h>

  typedef struct _Endereco Endereco;

  // registroCEP = struct.Struct("72s72s72s72s2s8s2s")

  struct _Endereco {
    char logradouro[72];
    char bairro[72];
    char cidade[72];
    char uf[72];
    char sigla[2];
    char cep[8];
    char lixo[2]; // Ao Espaço no final da linha + quebra de linha
  };

  long buscaBin(int inicio, int fim, FILE *f, char *cepProcurado, Endereco *e) {

    int meio;
    int cont = 0;

    while (inicio <= fim) {
      cont += 1;//quantos movimentos foram feitos
      meio = (inicio + fim) / 2;
      fseek(f, meio * sizeof(Endereco),SEEK_SET); // altera posição de leitura pro meio do arquivo
                       // (meio*sizeof)
      fread(e, sizeof(Endereco), 1,f); // Parametros: ponteiro generico p dado, tamanho de cada
                // unidade, total de unidades a serem lidas (só 1 cep) e o
                // ponteiro pro arquivo lido
      if (strncmp(cepProcurado, e->cep, 8) ==0) { // compara o cep do argumento de de linha com o cep
        //da struct
               // (atualmente no meio), o 8 pra informar quantos caracteres comparar //se cep prcurado ==
               // atual posicao (meio)
        return ftell(f); // mostra a parte q esta a posicao de leitura se o if der certo
      } else if (strncmp(cepProcurado, e->cep, 8) >0) { // se cep prcurado > atual posicao de leitura, inicio
                      // recebe meio + 1
        inicio = meio + 1;

      } else { // se cep procurado < posicao de leitura atual, fim recebe meio
               // -1
        fim = meio - 1;
      }
      printf("Passos: %d\n\n", cont);
    }
    return -1;
  }

  int main(int argc, char **argv) {
    FILE *f;  //arquivo que vai ser lido
    Endereco e;  //estrutura e
    int qt;
    long ta;
    long qr;

    if (argc != 2) {
      fprintf(stderr, "USO: %s [CEP]", argv[0]);
      return 1;
    }

    printf("Tamanho de cada Estrutura: %ld\n\n",sizeof(Endereco)); // calcula o tamanho de cada struct
    f = fopen("CEP_RJ_ORDENADO.dat", "rb");//cria um arquivo pra colocar o cep ordenado
    fseek(f, 0, SEEK_END); // calcula o tamanho total do arquivo
    ta = ftell(f);        //retorna tamamnho total do arquivo
    qr = ta / sizeof(Endereco); // qtd registros = tamanho do arquivo 
                                // /tamanho do registro
    printf("Tamanho total do arquivo: %ld\n\n",ta);                   
    int inicio = 0;
    int fim = qr - 1; //começa na posiçao 0
    long verdadeiro = buscaBin(inicio, fim, f, argv[1], &e);
    if (verdadeiro == -1) {
      printf("Nao encontrou\n");
    } else {
      printf("%.72s\n%.72s\n%.72s\n%.72s\n%.2s\n%.8s\n", e.logradouro, e.bairro,
             e.cidade, e.uf, e.sigla, e.cep);
    }
    fclose(f);
  }
