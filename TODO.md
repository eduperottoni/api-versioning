## Tarefas/requisitos pendentes:

- [x]  Juntar o `nginx` e as versões do `verifier` em um único serviço no `docker-compose`
    
    Ou seja, teremos uma máquina com `python`  e  `nginx` 
- [x]  Usar o `supervisord` como gerenciador de processos do container que executa as versões + `nginx`
    - [x] Criar um `script` que automatize a criação desse arquivo de configurações baseado nas versões que o usuário quer colocar no ar.
    - Sobre os _logs_ dos processos: 
       - [x] ver como o valor do campo `loglevel` influencia no _output_ desses _logs_
       - [x] inserir a versão junto ao _log_ do serviço

```bash
[2024-06-04 18:35:52,938] [VERSION] [INFO    ] [SNone UNone RNone] [root:config.py:101] No need to renew the 
```

- [x]  Unir os `bash scripts` em apenas 1 arquivo. Ou melhor: deixar de usar `bash scripts` e usar scripts `python`.
- [x]  ~~Não usar um arquivo específico para determinar as versões da biblioteca a serem usadas, mas sim variáveis de ambiente (que serão em arquivos).~~ (Vamos ter esse arquivo e ele é de suma importância para lidarmos com a `RFC 8594` e com outras questões dentro da construção do container)
    - A ideia aqui é que, tendo uma nova versão, essa versão a ser usada seja informada nas variáveis de ambiente do serviço
- [ ]  ~~Para o problema de variáveis de ambiente que mudam de nome ou de valor de acordo com a versão do serviço: ao invés de manter um arquivo para cada versão, manter um padrão de `override` de variáveis no compose.~~
    
    ~~O desafio aqui é  criar uma inteligência que considere o `override` inteligente (uma variável criada na versão 1_0_0 deve existir na versão 1_0_1 e pode ser modificada). Isso deve ser feito em `python` .~~
    
    ```docker
    document-verifier:
    	var1:
    	var2:
    	1_0_0: -< {
    		var1:
    		var2:
    	}
    	1_0_1: -< {
    		var1:
    	}
    ```
    
    - [x]  Trabalhar com as variáveis de ambiente em arquivos ao invés de apenas no `compose` . Ou seja, teremos um arquivo com variáveis de ambiente comuns e um arquivo para cada versão quando ocorre o override.
       - [x] Caso uma variável esteja com valor em branco em certa versão, daí em diante ela deve ser apagada.
       - [x] Criar um parâmetro que torne o `override` de variáveis de ambiente nesses arquivos opcional.
    - [x]  Criar um módulo/biblioteca `python` (`versionador`) que contenha esses scripts de interpretação de versões/variáveis de ambiente
- [x]  Quando prepara um `venv` para determinada versão, as variáveis de ambiente devem ser printadas no terminal
- [ ]  Quando for construir o ambiente virtual da versão 1.0.1, apontar para as bibliotecas que são iguais da versão anterior (1.0.0)
    
   - Isso deixará a imagem mais leve, economizará a memória ocupada pelas libs.
    
- [ ]  Ver o padrão de mercado para nome da rota do `reverse-proxy-api` que renderiza a documentação`swagger` (`/docs`, `/swagger`?)
- [x]  Usar o `nexus` para pegar versões das bibliotecas proprietárias utilizadas
    
    - Olhar CI/CD do commonlib (que publica no nexus).
    
    - Testar localmente o download de uma biblioteca

- [ ] Possibilitar execução de todos os testes para todas as versões dentro da instância
- [ ] Ver como controlar o supervisor para que o mesmo seja finalizado quando algum subprocesso der `EXIT`

- [ ] Sobre a documentação da `API` com `Swagger`, usar uma biblioteca que gere a documentação a partir do código fonte
   - Aqui, a ideia é que não tenhamos o `Swagger` como um arquivo separado, mas sim que esse arquivo seja criado dinamicamente.
   - O Swagger deve ser gerado na ponta, para cada instância. Cada uma vasi ter suas datas de `sunset`, `deprecated`, etc.
   - Seria interessante ter um índice de versões. Esse índice carregaria as informações contidas no arquivo de versões.

- Sobre o versionamento em geral, ele deve ser uma estrutura que pode ser replicada nas pontas (ambientes de produção de cada uma das instâncias).
   - [ ] Replicar a estrutura de versionamento para cada uma das instâncias do projeto, visto que cada uma pode ter suas próprias versões.

- Sobre a `RFC 8594`:
   - [ ] Trabalhar com campos de lançamento, data de depreciação e sunset dentro do arquivo de versões em `yml`:
   - [ ] Uma chamada em versões depreciadas deve retornar um código de erro e um cabeçalho com um _link_ para a documentação dos serviços.
   - [ ] Usar o arquivo de versões para gerar o `nginx`, de forma a ver quais versões estão depreciadas e quais ainda devem ser mantidas no ar por esse arquivo.

```yaml
1.0.0:
  active-date: 01/05/2024 08:00:00 GMT
  deprecated-date: 01/08/2024 08:00:00 GMT
  sunset-date: 01/09/2024 08:00:00 GMT
```