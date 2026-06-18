# UserManager — Projeto Android Acadêmico

## 📋 Índice
1. [Estrutura do Projeto](#estrutura-do-projeto)
2. [Dependências Gradle](#dependências-gradle)
3. [Passo a Passo de Instalação](#passo-a-passo-de-instalação)
4. [Como Executar no Android Studio](#como-executar-no-android-studio)
5. [Usuário Administrador Padrão](#usuário-administrador-padrão)
6. [Como Testar Todas as Funcionalidades](#como-testar-todas-as-funcionalidades)
7. [Possíveis Erros e Soluções](#possíveis-erros-e-soluções)
8. [Estrutura do Banco de Dados](#estrutura-do-banco-de-dados)
9. [Arquitetura MVVM](#arquitetura-mvvm)

---

## Estrutura do Projeto

```
UserManagerApp/
├── build.gradle                          # Config raiz do projeto
├── settings.gradle                       # Módulos do projeto
├── gradle.properties                     # Propriedades globais
├── gradle/wrapper/
│   └── gradle-wrapper.properties        # Versão do Gradle
└── app/
    ├── build.gradle                      # Dependências do módulo
    ├── proguard-rules.pro
    └── src/main/
        ├── AndroidManifest.xml           # Declaração de Activities
        ├── java/com/academico/usermanager/
        │   ├── activities/
        │   │   ├── SplashActivity.kt     # Tela inicial (2s) + check de sessão
        │   │   ├── LoginActivity.kt      # Tela de autenticação
        │   │   ├── RegisterActivity.kt   # Auto-cadastro de usuário
        │   │   ├── HomeActivity.kt       # Home do usuário comum
        │   │   ├── AdminActivity.kt      # Painel administrativo (CRUD)
        │   │   └── UserFormActivity.kt   # Formulário criar/editar usuário
        │   ├── viewmodels/
        │   │   └── UserViewModel.kt      # MVVM ViewModel (lógica UI)
        │   ├── repositories/
        │   │   └── UserRepository.kt     # Repository Pattern (acesso a dados)
        │   ├── models/
        │   │   └── User.kt               # Entidade Room / modelo de dados
        │   ├── database/
        │   │   ├── AppDatabase.kt        # Singleton do banco Room
        │   │   └── UserDao.kt            # Interface DAO (queries SQL)
        │   ├── adapters/
        │   │   └── UserAdapter.kt        # RecyclerView adapter (lista de users)
        │   └── utils/
        │       ├── HashUtils.kt          # Criptografia SHA-256 para senhas
        │       ├── SessionManager.kt     # Controle de sessão (SharedPreferences)
        │       └── ValidationUtils.kt    # Validações de formulário
        └── res/
            ├── layout/
            │   ├── activity_splash.xml
            │   ├── activity_login.xml
            │   ├── activity_register.xml
            │   ├── activity_home.xml
            │   ├── activity_admin.xml
            │   ├── activity_user_form.xml
            │   └── item_user.xml         # Layout do item da RecyclerView
            ├── values/
            │   ├── strings.xml
            │   ├── colors.xml
            │   └── themes.xml
            └── drawable/
                ├── ic_arrow_back.xml
                ├── ic_launcher_foreground.xml
                ├── spinner_background.xml
                └── badge_background.xml
```

---

## Dependências Gradle

No arquivo `app/build.gradle`, já estão configuradas:

```gradle
// Room Database (banco local)
implementation 'androidx.room:room-runtime:2.6.1'
implementation 'androidx.room:room-ktx:2.6.1'
kapt 'androidx.room:room-compiler:2.6.1'

// ViewModel + LiveData (MVVM)
implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0'
implementation 'androidx.lifecycle:lifecycle-livedata-ktx:2.7.0'

// Material Design 3
implementation 'com.google.android.material:material:1.11.0'

// Coroutines (operações assíncronas)
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3'

// Splash Screen API
implementation 'androidx.core:core-splashscreen:1.0.1'
```

---

## Passo a Passo de Instalação

### Pré-requisitos
- Android Studio Hedgehog (2023.1.1) ou superior
- JDK 17 (já incluso no Android Studio moderno)
- Android SDK 34 instalado
- Emulador ou dispositivo físico com Android 7.0+ (API 24)

### Instalação

**1. Abrir o projeto:**
```
File → Open → Selecione a pasta UserManagerApp
```

**2. Aguardar sincronização:**
- O Android Studio irá baixar as dependências automaticamente
- Aguarde o "Gradle sync" completar (barra inferior)
- Primeira vez pode levar 3-5 minutos dependendo da internet

**3. Verificar SDK:**
```
File → Project Structure → SDK Location
Confirme que o Android SDK está apontando corretamente
```

**4. Sincronizar manualmente se necessário:**
```
File → Sync Project with Gradle Files
```

---

## Como Executar no Android Studio

### Usando Emulador

**1. Criar AVD (se não tiver):**
```
Tools → Device Manager → Create Device
Recomendado: Pixel 6, API 34 (Android 14)
```

**2. Executar:**
```
Run → Run 'app'   (ou Shift+F10)
Selecione o emulador na lista
```

### Usando Dispositivo Físico

**1. Ativar Opções do Desenvolvedor:**
```
Configurações → Sobre o telefone → Número de compilação (toque 7x)
```

**2. Ativar Depuração USB:**
```
Configurações → Opções do desenvolvedor → Depuração USB → Ativar
```

**3. Conectar o cabo USB e autorizar o computador**

**4. Executar pelo Android Studio:**
```
Run → Run 'app'   (seu dispositivo aparecerá na lista)
```

---

## Usuário Administrador Padrão

O banco é populado automaticamente na primeira execução com:

| Campo | Valor |
|---|---|
| **Email** | `admin@admin.com` |
| **Senha** | `admin123` |
| **Tipo** | Administrador |
| **Nome** | Administrador |

E um usuário comum de exemplo:

| Campo | Valor |
|---|---|
| **Email** | `arthur@email.com` |
| **Senha** | `senha123` |
| **Tipo** | Usuário Comum |
| **Nome** | Arthur Silva |

> **IMPORTANTE**: As senhas são armazenadas como hash SHA-256. No banco, você verá algo como `240be518...` — isso é normal e esperado.

---

## Como Testar Todas as Funcionalidades

### 1. Splash Screen
- Abra o app — a tela roxa aparece por 2 segundos
- Redireciona para Login automaticamente

### 2. Login como Usuário Comum
```
Email: arthur@email.com
Senha: senha123
```
- Deve aparecer a tela de boas-vindas com o nome "Bem-vindo, Arthur Silva!"
- Nenhuma funcionalidade de admin é visível

### 3. Login como Administrador
```
Email: admin@admin.com
Senha: admin123
```
- Deve aparecer o Painel Administrativo com a lista de usuários

### 4. Cadastro de Novo Usuário (auto-registro)
- Na tela de Login, toque em "Não tem conta? Cadastre-se"
- Preencha Nome, Email, Senha e Confirmar Senha
- Tente criar com email já cadastrado → deve mostrar erro
- Tente com senha menor que 5 caracteres → deve mostrar erro

### 5. CRUD pelo Admin

**Create (Criar):**
- No painel admin, toque no FAB "Novo Usuário" (+)
- Preencha os campos e selecione o tipo
- Toque em Salvar → usuário aparece na lista

**Read (Visualizar):**
- A lista no painel admin mostra todos os usuários em tempo real

**Update (Atualizar):**
- Toque em "Editar" em qualquer usuário
- Altere o nome/email/tipo
- Para manter a senha atual, deixe o campo em branco
- Toque em Salvar → lista atualiza automaticamente

**Delete (Excluir):**
- Toque em "Excluir" em qualquer usuário
- Confirme no diálogo que aparece
- Tente excluir o único administrador → deve exibir mensagem de erro

### 6. Logout
- Toque em "Sair" em qualquer tela
- Confirme → retorna para Login
- Feche e abra o app → deve pedir login novamente

### 7. Persistência de Sessão
- Faça login como admin
- Feche o app completamente (remova da lista de recentes)
- Abra novamente → deve ir direto para o painel admin

---

## Possíveis Erros e Soluções

### ❌ "Gradle sync failed"
**Solução:**
```
File → Invalidate Caches → Invalidate and Restart
Aguarde reiniciar e sincronizar novamente
```

### ❌ "SDK location not found"
**Solução:**
```
File → Project Structure → SDK Location
Configure o caminho correto do Android SDK
```

### ❌ "Cannot resolve symbol 'R'"
**Solução:**
```
Build → Clean Project
Build → Rebuild Project
```

### ❌ "kapt annotation processing failed"
**Causa**: Versão do Kotlin incompatível com Room
**Solução**: Confirme que `kotlin_version = '1.9.0'` no build.gradle raiz

### ❌ "MinSdk version mismatch"
**Solução**: Emulador ou dispositivo deve ser API 24+ (Android 7.0)

### ❌ App trava na Splash Screen
**Causa**: Banco de dados demorando para criar na primeira execução
**Solução**: Aguarde 3-5 segundos ou reinicie o app

### ❌ "Cannot access database on main thread"
**Causa**: Operação de banco sem coroutine
**Solução**: Todo acesso ao DAO usa `suspend fun` + `viewModelScope.launch`

### ❌ Lista de usuários não atualiza após edição
**Causa**: LiveData não está sendo observado corretamente
**Solução**: Confirme que `viewModel.todosUsuarios.observe(this) { ... }` está na `onCreate`

---

## Estrutura do Banco de Dados

### Tabela: `usuarios`

| Coluna | Tipo | Constraints | Descrição |
|---|---|---|---|
| `id` | INTEGER | PRIMARY KEY AUTOINCREMENT | ID único do usuário |
| `nome` | TEXT | NOT NULL | Nome completo |
| `email` | TEXT | NOT NULL, UNIQUE | Email de login |
| `senha` | TEXT | NOT NULL | Hash SHA-256 da senha |
| `tipo_usuario` | TEXT | NOT NULL, DEFAULT 'comum' | "admin" ou "comum" |

### Script SQL equivalente

```sql
CREATE TABLE IF NOT EXISTS usuarios (
    id          INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    nome        TEXT    NOT NULL,
    email       TEXT    NOT NULL,
    senha       TEXT    NOT NULL,
    tipo_usuario TEXT   NOT NULL DEFAULT 'comum',
    UNIQUE (email)
);

-- Índice gerado automaticamente pelo Room para a constraint UNIQUE
CREATE UNIQUE INDEX IF NOT EXISTS index_usuarios_email ON usuarios (email);

-- Dados iniciais (gerados pelo DatabaseCallback)
INSERT INTO usuarios (nome, email, senha, tipo_usuario)
VALUES (
    'Administrador',
    'admin@admin.com',
    -- SHA-256 de "admin123"
    '240be518fabd2724ddb6f04eeb1da5967448d7e831c08c8fa822809f74c720a9',
    'admin'
);
```

---

## Arquitetura MVVM

```
┌─────────────────────────────────────────────────────┐
│                    UI Layer                         │
│  Activities (Login, Home, Admin, UserForm...)       │
│  Adapters (UserAdapter + RecyclerView)              │
└──────────────────────┬──────────────────────────────┘
                       │ observa LiveData
                       ▼
┌─────────────────────────────────────────────────────┐
│                 ViewModel Layer                     │
│  UserViewModel                                      │
│  - loginResult: LiveData<LoginResult>               │
│  - todosUsuarios: LiveData<List<User>>              │
│  - cadastroResult, atualizacaoResult, exclusaoResult│
└──────────────────────┬──────────────────────────────┘
                       │ chama suspend functions
                       ▼
┌─────────────────────────────────────────────────────┐
│                Repository Layer                     │
│  UserRepository                                     │
│  - autenticar(), cadastrar(), atualizar(), deletar()│
│  - Aplica regras de negócio (hash, validações)      │
└──────────────────────┬──────────────────────────────┘
                       │ acessa
                       ▼
┌─────────────────────────────────────────────────────┐
│                 Data Layer                          │
│  UserDao (interface Room / queries SQL)             │
│  AppDatabase (Room Database singleton)              │
│  User.kt (entidade / modelo de dados)               │
└─────────────────────────────────────────────────────┘
```

### Fluxo de Login

```
LoginActivity
  → btnEntrar.click()
  → viewModel.fazerLogin(email, senha)
     → repository.autenticar(email, sha256(senha))
        → dao.autenticar(email, hash) [Room SQLite]
     ← User? (encontrado ou null)
  ← loginResult.postValue(Sucesso/Erro)
  → observe { result ->
      Sucesso → sessionManager.saveSession() → navegar
      Erro    → Snackbar com mensagem
    }
```

---

## Segurança Implementada

1. **Senhas hasheadas**: SHA-256 unidirecional — não é possível reverter
2. **Validação de sessão**: Toda Activity verifica `sessionManager.isLoggedIn()`
3. **Controle de acesso**: AdminActivity verifica `sessionManager.isAdmin()`
4. **Email único**: Constraint UNIQUE no banco impede duplicatas
5. **Proteção ao último admin**: Não é possível excluir o único administrador
6. **Backstack limpo**: Após login/logout, histórico de navegação é limpo

---

*Projeto desenvolvido com Android Studio + Kotlin + Room Database + MVVM*
*Material Design 3 | Arquitetura limpa e comentada*
