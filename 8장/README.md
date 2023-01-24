## 8장 - JUnit

- 전체 TDD 테스트하려면 jacoco 사용

```java
// Mockito를 사용하기 위한 어노테이션
@ExtendWith(MockitoExtension.class)
public class Test {
    // 목객체 선언
    @Mock
    public MarketServer marketServer;
    // marketServer.price함수 호출 이전에 실행
    @BeforeEach
    public void init(){
        Mockito.lenient().when(marketServer.price()).thenReturn(200);
    }

    @Test
    public void hello(){
        System.out.println("hello");
    }

    @Test
    public void dollar(){
        DollarCalculator dollarCalculator = new DollarCalculator(null);
        Calculator calculator = new Calculator(dollarCalculator);
        int sum = calculator.sum(100, 100);
        Assertions.assertEquals(20000, sum);
    }

    @Test
    public void mock(){
        DollarCalculator dollarCalculator = new DollarCalculator(marketServer);
        dollarCalculator.init();

        Calculator calculator = new Calculator(dollarCalculator);
        int sum = calculator.sum(100, 100);
        Assertions.assertEquals(40000, sum);
    }
}
```

```java
@WebMvcTest({CalculatorApiController.class})
@AutoConfigureMockMvc
// 필요한 클래스 import
@Import(DollarCalculator.class)
public class CalculatorApiControllerTest {

    @Autowired
    private MockMvc mockMvc;
    // 목 객체 선언
    @MockBean
    private MarketServer marketServer;

    @Test
    public void helloTest() throws Exception {
        mockMvc.perform(
                MockMvcRequestBuilders.get("/api/calculator/hello")
        ).andExpect(
                MockMvcResultMatchers.status().isOk()
        ).andExpect(
                content().string("hello")
        ).andDo(MockMvcResultHandlers.print());
    }

    @Test
    public void getTest() throws Exception {
        // Mock으로 marketServer의 price함수가 호출될 시 1100 리턴
        when(marketServer.price()).thenReturn(1100);

        LinkedMultiValueMap<String, String> map = new LinkedMultiValueMap();
        map.add("x","10");
        map.add("y","10");

        mockMvc.perform(
                MockMvcRequestBuilders.get("/api/calculator/sum").queryParams(map)
        ).andExpect(
                MockMvcResultMatchers.status().isOk()
        ).andExpect(
                content().string("22000")
        ).andDo(MockMvcResultHandlers.print());
    }

    @Test
    public void postTest() throws Exception {
        when(marketServer.price()).thenReturn(1100);

        CalculatorReq req = new CalculatorReq();
        req.setX(10);
        req.setY(10);

        String json = new ObjectMapper().writeValueAsString(req);
        System.out.println(json);

        mockMvc.perform(
                MockMvcRequestBuilders.post("/api/calculator/sum")
                .content(json)
                .contentType("application/json")
                .accept("application/json")
        ).andExpect(
                MockMvcResultMatchers.status().isOk()
        ).andExpect(
                MockMvcResultMatchers.jsonPath("$.result").value("22000")
        ).andExpect(
                MockMvcResultMatchers.jsonPath("$.body.responseResult").value("22000")
        ).andDo(MockMvcResultHandlers.print());
    }

}
```