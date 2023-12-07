# rest-demo
1. Using DataLoader extends CommandLineRunner to populate DB when app runs
2. Isolated unit test for @Repository using @DataJpaTest and inmemory DB H2
3. Unit test for @Service layer using @ExtendWith(MockitoExtension.class)
4. Isolated unit test for @RestController using @WebMvcTest(CatController.class) and ObjectMapper (also with manual configuration)
5. Integration tests for @RestController
   
NOTES
==============================================================================================================================================================================
A unit test tests the smallest functional piece of code for 
⦁	returned values
⦁	thrown exceptions
⦁	data modification 
⦁	the behavior of a specific controller in isolation
⦁	the behavior of a specific repository in isolation

For testing isolated @Repository: 
@DataJpaTest
@AutoConfigureTestDatabase(connection = EmbeddedDatabaseConnection.H2) 
class CatRepositoryTest {
	@Autowire CatRepository catRepository;
	@Test
	void findById_validPayload_returnValidCats
}

For testing isolated @Service
(or any @Component that depends on a @Mock):
@ExtendWith(MockitoExtension.class)
class CatServiceImplTest {
	@Mock CatRepository catRepository
	@InjectMocks CatService catService;
	@Test
	void findById_validPayload_returnValidCatDto() {}

For testing isolated @Controller using MockMvc
⦁	manual configuration
@ExtendWith(MockitoExtension.class)
class CatControllerTest {
	@Mock CatService catService;
	@IntectMocks CatController catController;
	MockMvc mockMvc;
	DataBinder dataBinder;
	 @BeforeEach
	 void init() {
      	      mockMvc = MockMvcBuilders.standaloneSetup(catController).build;
      	      objectMapper= new ObjectMapper;
	@Test
	public void createCat_returnsValidEntity() throws Exception {}

⦁	automatic configuration
@WebMvcTest(CatController.class) // setups mockmvc and objectMapper
class CatControllerTest {
  	@Autowired MockMvc mockMvc;
	@MockBean CatService catService; //  integrates catService into Spring
  	@Autowired ObjectMapper objectMapper;
  	@Test
  	void createCat_returnsValidEntity() throws Exception {}

An integration test (not isolated) tests throughout whole application.
It uses @SpringBootTest, and involves interracting real DB, so if something changes in DB, tests should be adopted. Integration tests are usually very fragile. Moreover test data should be purged after each test (using @AfterEach void tearDown() {} )

For testing integrated @Controller using MockMvc
@SpringBootTest // integrates all @Component into Spring
@AutoConfigureMockMvc(printOnlyOnFailure = false)
class CatControllerIntegrationTest {
	@Autowired MockMvc mockMvc;
	@Autowired CatService catService;
	@Test
	void createCat_returnsValidEntity() throws Exception {}
