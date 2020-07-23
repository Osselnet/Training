# Spring
+ [Что такое инверсия контроля (IoC) и внедрение зависимостей (DI)? Как эти принципы реализованы в Spring?](#Что-такое-sql)
+ [Какие существуют операторы SQL?](#Какие-существуют-операторы-sql)

## Что такое IoC и DI?
Dependency injection (DI) - паттерн проектирования и архитектурная модель, так же известная как Inversion of Control (IoC). DI описывает ситуацию, когда один объект реализует свой функционал через другой объект. Например, соединение с базой данных передается конструктору объекта через аргумент, вместо того чтобы конструктор сам устанавливал соединение. Существуют три формы внедрения (но не типа) зависимостей: сэттер, конструктор и внедрение путем интерфейса.

DI - это способ достижения слабой связанности. IoC предоставляет возможность объекту получать ссылки на свои зависимости. Обычно это реализуется через lookup-метод. Преимущество IoC в том, что эта модель позволяет отделить объекты от реализации механизмов, которые он использует. В результате мы получаем большую гибкость как при разработке приложений, так и при их тестировании.

[к оглавлению](#Spring)


Что такое бин?
2. Виды бинов?
3. Чем бин отличается от POJO-класса?
4. Что такое Inversion of Control и как Spring реализует этот принцип?
5. Для чего существует такое количество ApplicationContext?
6. Как можно связать бины?
7. Что такое Dependency Injection?
8. Какие бины будут использоваться для настройки приложения?

## Что такое Inversion of Control и как Spring реализует этот принцип?
Ключевым элементом Spring Framework является Spring Container. Container создаёт объекты, связывает их вместе, настраивает и управляет ими от создания до момента уничтожения.

Для управления компонентами, из которых состоит приложение, Spring Container использует Внедрение Зависимостей (DI). Эти объекты называются Spring Beans, которые мы обсудим далее.

Spring Container получает инструкции какие объекты инстанциировать и как их конфигурировать через метаданные.

Метаданные могут быть получены 3 способами:
1. XML
2. Аннотации Java
3. Java код

Java POJO классы поступает в Spring Container, который на основании инструкций, полученных через метаданные, выдаёт приложение, готовое к использованию.

В Spring имеется 2 различных вида контейнеров:
1. Spring BeanFactory Container;
2. Spring ApplicationContext Container;

Spring BeanFactory Container

Это самый простой контейнер, который обеспечивает базовую поддержку DI и который основан на интерфейсе org.springframework.beans.factory.BeanFactory. Такие интерфейсы, как BeanFactoryAware и DisposableBean всё ещё присутствуют в Spring для обеспечения обратной совместимости.
Наиболее часто используемая реализация интерфейса BeanFactory – XmlBeanFactory.
XmlBeanFactory получает метаданные из конфигурационного XML файла и использует его для создания настроенного приложения или системы.

BeanFactory обычно используется тогда, когда ресурсы ограничены (мобильные устройства). Поэтому, если ресурсы не сильно ограничены, то лучше использовать ApplicationContext.

Использование @Bean аннотации
Как упоминалось выше, для того, чтобы объявить Bean-объект(далее просто бин), достаточно указать аннотацию @Bean тому методу, который возвращает тип бина как в классах с аннотацией @Configuration, так и в классах с аннотацией @Component(или её наследниках). Например, определим интерфейс какого-нибудь сервиса и его реализацию:

    package lessons.services;
    
    public interface GreetingService {
        String sayGreeting();
    }
    src/main/java/lessons/services/GreetingServiceImpl.java
    
    package lessons.services;
    
    public class GreetingServiceImpl implements GreetingService {
    
        @Override
        public String sayGreeting() {
            return "Greeting, user!";
        }
    }
Теперь, для того, чтобы объект с типом GreetingService был доступен для использования, необходимо описать его в конфигурации следующим образом:


    @Configuration
    public class LessonsConfiguration {
        @Bean
        GreetingService greetingService() {
            return new GreetingServiceImpl();
        }
    }
А для того, чтобы использовать его, достаточно выполнить следующее:


    public class Starter {
        private static final Logger logger = LogManager.getLogger(Starter.class);
    
        public static void main(String[] args) {
            logger.info("Starting configuration...");
    
            ApplicationContext context = new AnnotationConfigApplicationContext(LessonsConfiguration.class);
            GreetingService greetingService = context.getBean(GreetingService.class);
            logger.info(greetingService.sayGreeting());  // "Greeting, user!"
        }
    }
Метод getBean() может принимать в качестве аргумента как класс(как показано выше), так и названия бина, либо другие варианты. Однако такой подход не рекомендуется использовать в production-конфигурациях, т.к. для подобных целей существует механизм Dependency Injection (DI), собственно говоря, для чего и предназначен Spring IoC контейнер.

Использование @Bean аннотации
Как упоминалось выше, для того, чтобы объявить Bean-объект(далее просто бин), достаточно указать аннотацию @Bean тому методу, который возвращает тип бина как в классах с аннотацией @Configuration, так и в классах с аннотацией @Component(или её наследниках). Например, определим интерфейс какого-нибудь сервиса и его реализацию:

    
    package lessons.services;
    
    public interface GreetingService {
        String sayGreeting();
    }
    

    package lessons.services;
    
    public class GreetingServiceImpl implements GreetingService {
    
        @Override
        public String sayGreeting() {
            return "Greeting, user!";
        }
    }
Теперь, для того, чтобы объект с типом GreetingService был доступен для использования, необходимо описать его в конфигурации следующим образом:

    @Configuration
    public class LessonsConfiguration {
        @Bean
        GreetingService greetingService() {
            return new GreetingServiceImpl();
        }
    }
А для того, чтобы использовать его, достаточно выполнить следующее:

    public class Starter {
    
        private static final Logger logger = LogManager.getLogger(Starter.class);
    
        public static void main(String[] args) {
            logger.info("Starting configuration...");
    
            ApplicationContext context = new AnnotationConfigApplicationContext(LessonsConfiguration.class);
            GreetingService greetingService = context.getBean(GreetingService.class);
            logger.info(greetingService.sayGreeting());  // "Greeting, user!"
        }
    }
Метод getBean() может принимать в качестве аргумента как класс(как показано выше), так и названия бина(подробнее будет рассмотрено ниже), либо другие варианты, с которыми вы можете ознакомится в документации. Однако такой подход не рекомендуется использовать в production-конфигурациях, т.к. для подобных целей существует механизм Dependency Injection (DI), собственно говоря, для чего и предназначен Spring IoC контейнер.

Области видимости(scopes) бинов
Когда вы создаете определение бинов, вы вы создаете рецепт для создания экземпляров класса, который определяет бин. Важно понять, что определение бинов является рецептом, потому что он означает, какого класса вы можете создать множество экземпляров по этому рецепту.

Вы можете контролировать не только какие зависимости и значения конфигурации вы можете подключить в объекте, который создан из определения бина, но также область видимости из того же определения бина. Это мощный и гибкий подход, при котором вы можете выбрать область видимости создаваемых объектов. Изначально, Spring Framework поддерживает несколько вариантов, некоторые доступны, только если вы используете web-aware ApplicationContext. Также вы можете создать свою собственную облать видимости. Ниже приведен список областей видимостей, описанных в документации на момент написания урока:

+ singleton - По умолчанию. Spring IoC контейнер создает единственный экземпляр бина. Как правило, используется для бинов без сохранения состояния(stateless)
+ prototype - Spring IoC контейнер создает любое количество экземпляров бина. Новый экземпляр бина создается каждый раз, когда бин необходим в качестве зависимости, либо через вызов getBean(). Как правило, используется для бинов с сохранением состояния(stateful)
+ request - Жизненный цикл экземпляра ограничен единственным HTTP запросом; для каждого нового HTTP запроса создается новый экземпляр бина. Действует, только если вы используете web-aware ApplicationContext
+ session - Жизненный цикл экземпляра ограничен в пределах одной и той же HTTP Session. Действует, только если вы используете web-aware ApplicationContext
+ global session - Жизненный цикл экземпляра ограничен в пределах глобальной HTTP Session(обычно при использовании portlet контекста). Действует, только если вы используете web-aware ApplicationContext
+ application - Жизненный цикл экземпляра ограничен в пределах ServletContext. Действует, только если вы используете web-aware ApplicationContext

С более подробной информацией о настройке приложения для применения областей видимости request, session, global session и application вы можете ознакомиться в документации. Пример реализации собственной области видимости будет рассмотрено в отдельном уроке.

Для того, чтобы указать область видимости бина, отличный от singleton, необходимо добавить аннотацию @Scope("область_видимости") методу объявления бина или классу с аннотацией @Component:

    @Component
    @Scope("prototype")
    public class GreetingServiceImpl implements GreetingService {
        //...
    }
Использование @Configuration аннотации
Как упоминалось выше, классы с аннотацией @Configuration указывают на то, что они являются источниками определения бинов, public-методов с аннотацией @Bean.

Кода бин имеет зависимость от другого бина, то зависимость выражается просто как вызов метода:

    @Configuration
    @ComponentScan
    public class LessonsConfiguration {
        @Bean
        BeanWithDependency beanWithDependency() {
            return new BeanWithDependency(greetingService());
        }
    
        @Bean
        GreetingService greetingService() {
            return new GreetingServiceImpl();
        }
    }
Однако работает такое взаимодействие только в @Configuration-классах, в @Component-классах такое не работает.

Представим теперь ситуацию, когда у вас есть бин с областью видимости singleton, который имеет зависимость от бина с областью видимости prototype.

    public abstract class CommandManager {
        protected abstract Object createCommand();
    }
    
    @Configuration
    @ComponentScan
    public class LessonsConfiguration {
        @Bean
        @Scope("prototype")
        public Object asyncCommand() {
            return new Object();
        }
    
        @Bean
        public CommandManager commandManager() {
            // возвращаем новую анонимную реализацию CommandManager
            // с новым объектом
            return new CommandManager() {
                protected Object createCommand() {
                    return asyncCommand();
                }
            };
        }
    }
Большая часть приложений строится по модульной архитектуре, разделенная по слоям, например DAO, сервисы, контроллеры и др. Создавая конфигурацию, можно также её разбивать на составные части, что также улучшит читабельность и панимание архитектуры вашего приложения. Для этого в конфигурацию необходимо добавить аннотацию @Import, в параметрах которой указываются другие классы с аннотацией @Configuration, например:

    @Configuration
    public class AnotherConfiguration {
        @Bean
        BeanWithDependency beanWithDependency() {
            return new BeanWithDependency();
        }
    }
        @Configuration
        @ComponentScan
        @Import(AnotherConfiguration.class)
        public class LessonsConfiguration {
            @Bean
            GreetingService greetingService() {
                return new GreetingServiceImpl();
            }
        }
Таким образом, при инициализации контекста вам не нужно дополнительно указывать загрузку из конфигурации AnotherConfiguration, все останется так, как и было:

    public class Starter {
    
        private static final Logger logger = LogManager.getLogger(Starter.class);
    
        public static void main(String[] args) {
            logger.info("Starting configuration...");
    
            ApplicationContext context = new AnnotationConfigApplicationContext(LessonsConfiguration.class);
            GreetingService greetingService = context.getBean(GreetingService.class);
            BeanWithDependency withDependency = context.getBean(BeanWithDependency.class);
            logger.info(greetingService.sayGreeting()); // "Greeting, user!"
            logger.info(withDependency.printText()); // "Some text!"
        }
    }
В большинстве случаев, имеются такие случаи, когда бин в одной конфигурации имеет зависимость от бина в другой конфигурации. Поскольку конфигурация является источником определения бинов, то разрешить такую зависимость не является проблемой, достаточно объявить поле класса конфигурации с аннотацией @Autowired(более подробно оисано в отдельной главе):

    @Configuration
    public class AnotherConfiguration {
    
        @Autowired GreetingService greetingService;
    
        @Bean
        BeanWithDependency beanWithDependency() {
            //что-нибудь делаем с greetingService...
            return new BeanWithDependency();
        }
    }
При этом LessonsConfiguration остается без изменений:

    @Configuration
    @ComponentScan
    @Import(AnotherConfiguration.class)
    public class LessonsConfiguration {
        @Bean
        GreetingService greetingService() {
            return new GreetingServiceImpl();
        }
    }
Классы с аннотацией @Configuration не стремятся на 100% заменить конфигурации на XML, при этом, если вам удобно или имеется какая-то необходимость в использовании XML конфигурации, то к вашей Java-конфигурации необходимо добавить аннотацию @ImportResource, в параметрах которой необходимо указать нужное вам количество XML-конфигураций. Выглядит это следующим способом:

    @Configuration
    @ImportResource("classpath:/lessons/xml-config.xml")
    public class LessonsConfiguration {
        @Value("${jdbc.url}")
        String url;
        //...
    }
    src/main/java/lessons/xml-config.xml
    
    <beans>
        <context:property-placeholder location="classpath:/jdbc.properties"/>
    </beans>
    jdbc.properties
    
    jdbc.url=jdbc:hsqldb:hsql://localhost/xdb
Процесс разрешения зависимостей
IoC контейнер выполняет разрешение зависимостей бинов в следующем порядке:

Создается и инициализируется ApplicationContext с метаданными конфигурации, которые описывают все бины. Эти метаданные могут быть описаны через XML, Java-код или аннотации
Для каждого бина и его зависимостей вычисляются свойства, аргументы конструктора или аргументы статического фабричного метода, либо обычного(без аргументов) конструктора. Эти зависимости предоставляются бину, когда он(бин) уже создан. Сами зависимости инициализируются рекурсивно, в зависимости от вложенности в себе других бинов. Например, при инициализации бина А, котый имеет зависимость В, а В зависит от С, сначала инициализируется бин С, потом В, а уже потом А
Каждому свойству или аргументу конструктора устанавливается значение или ссылка на другой бин в контейнере
Для каждого свойства или аргумента конструктора подставляемое значение конвертируется в тот формат, который указан для свойства или аргумента. По умолчанию Spring может конвертировать значения из строкового формата во все встроенные типы, такие как int, long, String, boolean и др.
Spring каждый раз при создании контейнера проверяет конфигурацию каждого бина. И только бины с областью видимости(scope) singleton создаются сразу вместе со своими зависимостями, в отличие от остальных, которые создаются по запросу и в соответствии со своей областью видимости. В случае цикличной зависимости(когда класс А требует экземпляр В, а классу В требуется экземпляр А) Spring IoC контейнер обнаруживает её и выбрасывает исключение BeanCurrentlyInCreationException.

Spring контейнер может разрешать зависимости между бинами через autowiring(далее, автоматическое связывание). Данный механизм основан на просмотре содержимого в ApplicationContext и имеет следующие преимущества:

Автоматическое связывание позволяет значительно сократить количество инструкций для указания свойств или аргументов конструктора
Автоматическое связывание позволяет обновлять конфигурацию, несмотря на развитие ваших объектов. К примеру, вам необходимо добавить зависимость в классе и эта зависимость может быть разрешена без необходимости модификации конфигурации. Поэтому автоматическое связывание может быть особенно полезным при разработке, не исключая возможность переключения на явное описание, когда кодовая база будет стабильна
Для того, чтобы воспользоваться механизмом автоматического связывания, Spring Framework предоставляет аннотацию @Autowired. Примеры применения приведены ниже:

    public class AutowiredClass {
    
        @Autowired //к полям класса
        @Qualifier("main")
        //@Autowired(required = false) //чтобы не бросалось исключение,
                                       //если не с кем связать
                                       //рекомендуется использовать @Required
        private GreetingService greetingService;
    
        @Autowired //к полям класса в виде массива или коллекции
        private GreetingService[] services;
    
        @Autowired //к Map, где ключами являются имена бинов, значения - сами бины
        private Map<String, GreetingService> serviceMap;
    
        @Autowired //к конструктору
        public AutowiredClass(@Qualifier("main") GreetingService service) {}
    
        @Autowired //к обычным методам с произвольным названием аргументов и их количеством
        public void prepare(GreetingService prepareContext){/* что-то делаем... */}
    
        @Autowired //к "традиционному" setter-методу
        public void setContext(GreetingService service) { this.greetingService = service; }
    }
Т.к. кандидатов для автоматического связывания может быть несколько, то для установки конкретного экземпляра необходимо использовать аннотацию @Qualifier, как показано ниже. Данная аннотация может быть применена как к отдельному полю класса, так и к отдельному аргументу метода или конструктора:

    public class AutowiredClass {
        //...
    
        @Autowired //к полям класса
        @Qualifier("main")
        private GreetingService greetingService;
    
        @Autowired //к отдельному аргументу конструктора или метода
        public void prepare(@Qualifier("main") GreetingService greetingService){
            /* что-то делаем... */
        };
    
        //...
    
    }
Соответственно, у одной из реализации GreetingService должна быть установлена соответствующая аннотация @Qualifier:

    @Component
    @Qualifier("main")
    public class GreetingServiceImpl implements GreetingService {
        //...
    }
    
    
1. Как получить данные из файла .property?
2. Как запустить Спринг-приложение из-под сервера Tomcat?
3. Что такое Artifacts?
4. В чем отличие артефакта war от war exploded?
5. Какая разница между аннотациями @Component, @Repository и @Service в Spring?
6. Как выглядит структура MVC-приложения?
7. Чем контроллер отличается от сервлета?
8. Какая основная зависимость фреймворка Спринг? Почему во многих сборках она не указывается явно?
9. Как вернуть страницу в контроллере? Как вернуть данные?

Разобранный архив - это дерево папок и файлов, которые соответствуют заданной структуре, которую ваш сервер приложений может использовать для развертывания приложения. Например, для веб-приложения вы создаете структуру каталогов war. Сервер приложений ожидает каталог WEB-INF, содержащий файлы web.xml, который выполняет дескриптор развертывания.

Упакованный архив - это zip-файл, содержащий вышеупомянутую структуру. Расширение упакованного архива может варьироваться (война, фляга, машина, ухо), но все они представляют собой zip-файлы, которые содержат данную структуру.

## В чём разница между @Component, @Service и @Repository аннотациями?
Все они определяют бины Spring. Однако между ними всё же есть разница.

+ @Component — универсальный компонент
+ @Repository — компонент, который предназначен для хранения, извлечения и поиска. Как правило, используется для работы с базами данных.
+ @Service — фасад для некоторой бизнес логики

Пользовательские аннотации, производные от @Component, могут добавлять специальную логику в бинах.

Например, бины, получившиеся при помощи @Repository, дополнительно имеют обработку для JDBC Exception

В Spring 2.0 и более поздних @Repository аннотация @Repository является маркером для любого класса, который выполняет роль или стереотип (также известный как объект доступа к данным или DAO) хранилища. Среди применений этого маркера - автоматический перевод исключений.

В Spring 2.5 @Component дополнительные аннотации стереотипов: @Component, @Service и @Controller. @Component - это общий стереотип для любого компонента, управляемого Spring. @Repository, @Service и @Controller являются специализациями @Component для более конкретных случаев использования, например, на уровнях постоянства, обслуживания и представления соответственно.

Таким образом, вы можете аннотировать классы компонентов с помощью @Component, но, вместо этого, аннотируя их с помощью @Repository, @Service или @Controller, ваши классы лучше подходят для обработки инструментами или связи с аспектами. Например, эти аннотации стереотипов делают идеальные цели для pointcut.

Таким образом, если вы выбираете между использованием @Component или @Service для своего уровня обслуживания, @Service, @Service является лучшим выбором. Точно так же, как указано выше, @Repository уже поддерживается в качестве маркера для автоматического преобразования исключений в вашем слое постоянства.

Вся логика работы Spring MVC построена вокруг DispatcherServlet, который принимает и обрабатывает все HTTP-запросы (из UI) и ответы на них. Рабочий процесс обработки запроса DispatcherServlet'ом проиллюстрирован на следующей диаграмме:


## Как выглядит структура MVC-приложения?
Ниже приведена последовательность событий, соответствующая входящему HTTP-запросу:

После получения HTTP-запроса DispatcherServlet обращается к интерфейсу HandlerMapping, который определяет, какой Контроллер должен быть вызван, после чего, отправляет запрос в нужный Контроллер.
Контроллер принимает запрос и вызывает соответствующий служебный метод, основанный на GET или POST. Вызванный метод определяет данные Модели, основанные на определённой бизнес-логике и возвращает в DispatcherServlet имя Вида (View).
При помощи интерфейса ViewResolver DispatcherServlet определяет, какой Вид нужно использовать на основании полученного имени.
После того, как Вид (View) создан, DispatcherServlet отправляет данные Модели в виде атрибутов в Вид, который в конечном итоге отображается в браузере.

Все вышеупомянутые компоненты, а именно, HandlerMapping, Controller и ViewResolver, являются частями интерфейса WebApplicationContext extends ApplicationContext, с некоторыми дополнительными особенностями, необходимыми для создания web-приложений.

## Основные паттерны Spring.
Вот некоторые известные паттерны, используемые в Spring Framework:

+ Proxy (Заместитель)
+ Singleton (Одиночка)
+ Factory (Фабрика)
+ Template (Шаблон)
+ Model View Controller (Модель-Представление-Контроллер)
+ Front Controller (Контроллер запросов)
+ View Helper (Вспомогательный компонент представления)
+ Dependency injection и Inversion of control (IoC) (Внедрение зависимостей и инверсия управления)
+ Service Locator (Локатор служб)
+ Observer-Observable (Наблюдатель)
+ Context Object (Контекстный объект)

