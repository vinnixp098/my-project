# Documentação da arquitetura do React Native 

A estrutura de pastas utilizada nos meus projetos React e React native geralmente seguem essa estrutura:

![image](https://github.com/user-attachments/assets/3f655318-364b-44ae-91e0-2ab75ae23466)

Para entender melhor o porquê dessa disposição de pastas, devemos entender como a arquitetura funciona. Abaixo, temos a imagem que mostra a estrutura da arquitetura:

![Estrutura da Arquitetura](https://raw.githubusercontent.com/miqueiasrodrigues/front-ent-react/main/arquitetura.drawio.png)

Devemos entender que a interface visualizada pelo usuário não é composta por um único e grande componente, mas por vários componentes menores, montados como peças de um quebra-cabeça que se encaixam para formar uma página completa. Por isso, a junção de vários componentes forma a view, que é a porta de entrada para que o usuário utilize nossa aplicação e interaja com o backend.

Mas essas informações, para irem para o backend ou para saírem do backend e irem para os componentes, devem ser tratadas. Ninguém gostaria de receber valores e mensagens que não entendem. Por isso temos os handlers. Eles são basicamente manipuladores: pegam a informação, direcionam para o serviço adequado e devolvem-na tratada.

Já os serviços são os responsáveis por pegar e enviar as informações para o nosso backend; eles são a porta de saída para o mundo externo. Dessa forma, os serviços pegam a informação, tratam-na e depois enviam para o backend, ou fazem o inverso: pegam a informação do backend, tratam-na e depois enviam para o handler.

Os models são as interfaces e tipos que a nossa aplicação utiliza. As interfaces possuem 2 tipos: entidades e abstratas. Interfaces abstratas geralmente são usadas para criar um componente ou estão relacionadas a composição de varios objetos. Já as interfaces do tipo entidade geralmente irão formar o nosso handler e estão relacionadas aas entidades do banco de dados. Já os tipos são valores que uma variável pode receber que não são primitivas. 

Exemplo de tipos:

```typescript

type StatusType = 'error' | 'success'
```

Exemplo de interface:

```typescript
// Interfaces abstratas geralmente são usadas para criar um componente
// ou estão relacionadas a composição de varios objetos.
interface Abstrato {
    texto: string;
    valor: number;
}

// Interfaces do tipo entidade geralmente irão formar o nosso handler
// e estão relacionadas aos modelos do banco de dados.
interface Entidade {
    id: number;
    nome: string;
}
```

Vamos entender como o componente forma a view. Suponha que criamos uma página ou tela de login; essa página terá vários componentes, como, por exemplo, um componente de botão, um campo de input de imagem, entre outros. Vamos usar como exemplo o componente de botão, que geralmente tem o seguinte formato:

```typescript

interface BotaoPropriedade {
  nome: string;
  acao?: () => void;
}

const ComponenteBotao: React.FC<BotaoPropriedade> = (props) => {
  return <button onClick={acao}>{props.nome}</button>;
};

```

Observamos que temos uma interface abstrata chamada BotaoPropriedade. Em nossa página, chamamos esse componente de botão e podemos associar um manipulador para tratar esses dados, como uma ação para o botão, que será executada quando o botão for clicado. Se estamos falando de sessão, e a sessão contém os dados do usuário, então, para facilitar a manipulação dessas informações, podemos criar uma interface de usuário que é do tipo entidade:


```typescript
interface Usuario {
    id: number;
    nome: string;
    email: string;
}
```

Agora vamos criar nosso manipulador de sessão

```typescript

export const manipuladorDeSessao = async (
  method: string = "GET",
  id: string | number| null = null,
  dados: any = [],
): Promise<any> => {

  if (method === "GET") {
   
  }


 if (method === "GET" && id !== null) {

  }

  //Implementação de outras lógicas


  return null;
};

```

Podemos melhorar o nosso handler e as nossas respostas entre o handler e o componente. Vamos criar uma interface de resposta para saber qual foi a situação do nosso serviço e se a requisição ocorreu com sucesso ou não.


```typescript

import { StatusType } from "../types/StatusType";

export interface ResponseInterface {
    status: StatusType;
    message: string;
    data?: any;
};

```

O HttpType inclui os métodos mais comuns: GET, POST, PUT, PATCH e DELETE. Existem outros métodos, mas não vamos abordá-los aqui.

```typescript
export type HttpType = "GET" | "POST" | "PUT" | "PATH" |  "UPDATE" | "DELETE";
```
Com base nisso, o nosso manipulador ficará assim:


```typescript

export const manipuladorDeSessao = async (
  method: HttpType = "GET",
  id: string | number | null = null,
  data: any = [],
): Promise<ResponseInterface> => {


 if (method === "POST") {

  }

  //Implementação de outros serviços


 return {
    status: "error",
    message: "O Método não foi mapeado.",
    data: null,
  };
};

```

Vamos criar um serviço para mostrar o usuário; ele se conectará com a nossa API:

```typescript
export const servicoDeCriarSessao = async (
  data?: any
): Promise<ResponseInterface> => {
  try {
    const response = await fetch(`${config.host}/v1/session`, {
      method: 'POST', 
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(data)
    });

    const responseData: ResponseInterface = await response.json();

    if (responseData.status === "error") {
      // Outra tratativa
      return responseData;
    }

    return responseData;
  } catch (error) {
    return {
      status: "error",
      message: "Erro na comunicação com o servidor.",
      data: null,
    };
  }
};
```


Pronto, temos o serviço de criar sessão em nosso manipulador. Agora podemos mapear esse serviço em nosso manipulador:


```typescript

export const manipuladorDeSessao = async (
  method: HttpType = "GET",
  id: string | number | null = null,
  data: any = [],
): Promise<ResponseInterface> => {


 if (method === "POST") {
       return await servicoDeCriarSessao(id);
  }

  //Implementação de outros serviços


 return {
    status: "error",
    message: "O Método não foi mapeado.",
    data: null,
  };
};

```


agora podemos chamar o nosso manipulador no componente do botão



```typescript

<ComponenteBotao nome="Login" acao={() => await manipuladorDeSessao("POST", null, dadosDoLogin)}/>

```


Depois disso podemos chamar o redux para guardar informaçoes que serão usados, como token ou chave de acesso:


consulte a documentação do redux:

https://redux-toolkit.js.org/usage/usage-guide




