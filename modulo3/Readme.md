# Design Patterns em Java Spring Boot

## Padrões de Criação

### Singleton
O padrão Singleton garante que uma classe tenha apenas uma instância e fornece um ponto global de acesso a ela.

#### Descrição
É útil quando é necessário garantir que apenas uma instância de uma classe seja criada, como em gerenciadores de configuração, conexões de banco de dados ou registradores de log.

#### Exemplo Prático
```java
@Component
public class ConfigurationManager {
    private static ConfigurationManager instance;
    private Properties properties;

    private ConfigurationManager() {
        properties = new Properties();
        properties.setProperty("app.name", "MyApp");
    }

    public static synchronized ConfigurationManager getInstance() {
        if (instance == null) {
            instance = new ConfigurationManager();
        }
        return instance;
    }

    public String getProperty(String key) {
        return properties.getProperty(key);
    }
}
```

#### Caso de Uso
Um gerenciador de configuração centralizado que carrega e fornece acesso a configurações da aplicação.

---

### Builder
O padrão Builder separa a construção de um objeto complexo da sua representação, permitindo a criação passo a passo.

#### Descrição
Ideal para criar objetos com muitas propriedades opcionais ou configurações complexas, como objetos de configuração ou relatórios.

#### Exemplo Prático
```java
public class User {
    private String username;
    private String email;
    private String phone;

    private User(Builder builder) {
        this.username = builder.username;
        this.email = builder.email;
        this.phone = builder.phone;
    }

    public static class Builder {
        private String username;
        private String email;
        private String phone;

        public Builder setUsername(String username) {
            this.username = username;
            return this;
        }

        public Builder setEmail(String email) {
            this.email = email;
            return this;
        }

        public Builder setPhone(String phone) {
            this.phone = phone;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}
```

#### Caso de Uso
Construção de objetos de usuário em um sistema de cadastro, onde nem todos os campos são obrigatórios.

---

### Factory Method
O padrão Factory Method define uma interface para criar objetos, mas permite que as subclasses decidam qual classe instanciar.

#### Descrição
Útil para criar objetos sem expor a lógica de criação ao cliente, como em sistemas que suportam múltiplos formatos de arquivo.

#### Exemplo Prático
```java
public interface Notification {
    void send(String message);
}

public class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}

public class SMSNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

public abstract class NotificationFactory {
    public abstract Notification createNotification();
}

@Component
public class EmailNotificationFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new EmailNotification();
    }
}

@Component
public class SMSNotificationFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new SMSNotification();
    }
}
```

#### Caso de Uso
Um sistema de notificações que pode enviar mensagens por diferentes canais, como e-mail ou SMS.

---

### Abstract Factory
O padrão Abstract Factory fornece uma interface para criar famílias de objetos relacionados ou dependentes sem especificar suas classes concretas.

#### Descrição
Ideal para sistemas que precisam ser independentes de como seus objetos são criados, como interfaces gráficas multiplataforma.

#### Exemplo Prático
```java
public interface UIFactory {
    Button createButton();
    Menu createMenu();
}

public class WindowsUIFactory implements UIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Menu createMenu() {
        return new WindowsMenu();
    }
}

public class MacUIFactory implements UIFactory {
    @Override
    public Button createButton() {
        return new MacButton();
    }

    @Override
    public Menu createMenu() {
        return new MacMenu();
    }
}
```

#### Caso de Uso
Criação de interfaces gráficas que funcionam tanto em Windows quanto em macOS.

---

## Padrões Estruturais

### Adapter
O padrão Adapter permite que interfaces incompatíveis trabalhem juntas.

#### Descrição
Útil para integrar sistemas legados com novos sistemas ou APIs.

#### Exemplo Prático
```java
public interface PaymentProcessor {
    void processPayment(double amount);
}

public class LegacyPaymentSystem {
    public void makePayment(double amount) {
        System.out.println("Payment of $" + amount + " processed by legacy system.");
    }
}

@Component
public class PaymentAdapter implements PaymentProcessor {
    private final LegacyPaymentSystem legacyPaymentSystem;

    public PaymentAdapter(LegacyPaymentSystem legacyPaymentSystem) {
        this.legacyPaymentSystem = legacyPaymentSystem;
    }

    @Override
    public void processPayment(double amount) {
        legacyPaymentSystem.makePayment(amount);
    }
}
```

#### Caso de Uso
Integração de um sistema de pagamento legado com uma nova interface de processamento.

---

### Facade
O padrão Facade fornece uma interface simplificada para um subsistema complexo.

#### Descrição
Útil para reduzir a complexidade de interações com subsistemas, como em sistemas de gerenciamento de pedidos.

#### Exemplo Prático
```java
@Component
public class OrderFacade {
    private final InventoryService inventoryService;
    private final PaymentService paymentService;
    private final ShippingService shippingService;

    public OrderFacade(InventoryService inventoryService, PaymentService paymentService, ShippingService shippingService) {
        this.inventoryService = inventoryService;
        this.paymentService = paymentService;
        this.shippingService = shippingService;
    }

    public void placeOrder(String productId, double amount) {
        if (inventoryService.isAvailable(productId)) {
            paymentService.processPayment(amount);
            shippingService.shipProduct(productId);
        } else {
            System.out.println("Product not available.");
        }
    }
}
```

#### Caso de Uso
Simplificação do processo de pedidos em um sistema de e-commerce.

---

### Flyweight
O padrão Flyweight reduz o uso de memória compartilhando o máximo de dados possível com objetos semelhantes.

#### Descrição
Ideal para sistemas que precisam lidar com um grande número de objetos semelhantes, como renderização de gráficos.

#### Exemplo Prático
```java
public class Character {
    private final char symbol;
    private final String font;

    public Character(char symbol, String font) {
        this.symbol = symbol;
        this.font = font;
    }

    public void display(int size) {
        System.out.println("Character: " + symbol + ", Font: " + font + ", Size: " + size);
    }
}

@Component
public class CharacterFactory {
    private final Map<String, Character> characters = new HashMap<>();

    public Character getCharacter(char symbol, String font) {
        String key = symbol + font;
        characters.putIfAbsent(key, new Character(symbol, font));
        return characters.get(key);
    }
}
```

#### Caso de Uso
Renderização de texto em editores gráficos, onde cada caractere compartilha a mesma fonte.

---

### Composite
O padrão Composite compõe objetos em estruturas de árvore para representar hierarquias parte-todo.

#### Descrição
Útil para representar estruturas hierárquicas, como sistemas de arquivos ou menus.

#### Exemplo Prático
```java
public interface FileSystemComponent {
    void display();
}

public class File implements FileSystemComponent {
    private final String name;

    public File(String name) {
        this.name = name;
    }

    @Override
    public void display() {
        System.out.println("File: " + name);
    }
}

public class Folder implements FileSystemComponent {
    private final String name;
    private final List<FileSystemComponent> components = new ArrayList<>();

    public Folder(String name) {
        this.name = name;
    }

    public void add(FileSystemComponent component) {
        components.add(component);
    }

    @Override
    public void display() {
        System.out.println("Folder: " + name);
        for (FileSystemComponent component : components) {
            component.display();
        }
    }
}
```

#### Caso de Uso
Representação de diretórios e arquivos em um sistema de arquivos.

---

### Decorator
O padrão Decorator adiciona responsabilidades a um objeto dinamicamente.

#### Descrição
Ideal para adicionar funcionalidades a objetos sem alterar suas classes.

#### Exemplo Prático
```java
public interface DataSource {
    String readData();
}

public class FileDataSource implements DataSource {
    @Override
    public String readData() {
        return "File data";
    }
}

public abstract class DataSourceDecorator implements DataSource {
    protected final DataSource dataSource;

    public DataSourceDecorator(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}

public class EncryptionDecorator extends DataSourceDecorator {
    public EncryptionDecorator(DataSource dataSource) {
        super(dataSource);
    }

    @Override
    public String readData() {
        return "Encrypted(" + dataSource.readData() + ")";
    }
}
```

#### Caso de Uso
Adição de criptografia a um sistema de leitura de arquivos.

---

## Padrões Comportamentais

### Command
O padrão Command encapsula uma solicitação como um objeto, permitindo parametrizar clientes com diferentes solicitações.

#### Descrição
Útil para implementar filas de tarefas ou operações desfazer/refazer.

#### Exemplo Prático
```java
public interface Command {
    void execute();
}

public class PrintCommand implements Command {
    private final String message;

    public PrintCommand(String message) {
        this.message = message;
    }

    @Override
    public void execute() {
        System.out.println(message);
    }
}

@Component
public class CommandInvoker {
    private final List<Command> commandQueue = new ArrayList<>();

    public void addCommand(Command command) {
        commandQueue.add(command);
    }

    public void executeCommands() {
        for (Command command : commandQueue) {
            command.execute();
        }
        commandQueue.clear();
    }
}
```

#### Caso de Uso
Implementação de um sistema de fila de impressão.

---

### Template Method
O padrão Template Method define o esqueleto de um algoritmo em uma operação, postergando alguns passos para subclasses.

#### Descrição
Ideal para algoritmos que possuem uma estrutura fixa, mas permitem personalização de etapas.

#### Exemplo Prático
```java
public abstract class DataProcessor {
    public void process() {
        loadData();
        processData();
        saveData();
    }

    protected abstract void loadData();
    protected abstract void processData();
    protected abstract void saveData();
}

public class CSVDataProcessor extends DataProcessor {
    @Override
    protected void loadData() {
        System.out.println("Loading CSV data");
    }

    @Override
    protected void processData() {
        System.out.println("Processing CSV data");
    }

    @Override
    protected void saveData() {
        System.out.println("Saving CSV data");
    }
}
```

#### Caso de Uso
Processamento de diferentes formatos de dados, como CSV ou JSON.

---

### State
O padrão State permite que um objeto altere seu comportamento quando seu estado interno muda.

#### Descrição
Útil para máquinas de estado, como sistemas de autenticação ou fluxos de trabalho.

#### Exemplo Prático
```java
public interface State {
    void handle();
}

public class LoggedOutState implements State {
    @Override
    public void handle() {
        System.out.println("User is logged out.");
    }
}

public class LoggedInState implements State {
    @Override
    public void handle() {
        System.out.println("User is logged in.");
    }
}

@Component
public class UserContext {
    private State state;

    public void setState(State state) {
        this.state = state;
    }

    public void request() {
        state.handle();
    }
}
```

#### Caso de Uso
Gerenciamento de estados de login de um usuário.

---

### Strategy
O padrão Strategy define uma família de algoritmos, encapsula cada um deles e os torna intercambiáveis.

#### Descrição
Ideal para sistemas que precisam alternar entre diferentes algoritmos, como métodos de pagamento.

#### Exemplo Prático
```java
public interface PaymentStrategy {
    void pay(double amount);
}

public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using Credit Card.");
    }
}

public class PayPalPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paid $" + amount + " using PayPal.");
    }
}

@Component
public class PaymentContext {
    private PaymentStrategy strategy;

    public void setStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void executePayment(double amount) {
        strategy.pay(amount);
    }
}
```

#### Caso de Uso
Implementação de diferentes métodos de pagamento em um sistema de e-commerce.
