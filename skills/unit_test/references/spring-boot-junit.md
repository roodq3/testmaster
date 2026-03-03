# Spring Boot Unit Test Standards

## Test Layers

- **Service**: `@ExtendWith(MockitoExtension.class)` + `@Mock` + `@InjectMocks` — never use `@SpringBootTest`
- **Controller**: `@WebMvcTest(XxxController.class)` + `@MockBean`
- **Repository**: `@DataJpaTest` + `TestEntityManager`
- **Utilities**: Plain JUnit, no Spring annotations needed

## Mock Strategy

| Scenario | Approach |
|----------|----------|
| Service dependencies | `@Mock` + `@InjectMocks` |
| Service in Controller | `@MockBean` |
| External HTTP | MockBean RestTemplate or WireMock |
| Static methods | `Mockito.mockStatic()` |
| Current time | Inject `Clock` or mockStatic |

## Must Follow

- given-when-then structure
- Use AssertJ (`assertThat`) over JUnit native assertions
- `@DisplayName` should clearly describe behavior
- `@ParameterizedTest` for multiple inputs
- Only verify critical interactions, don't verify every call

## Common Pitfalls

- **Never use `@SpringBootTest` for unit tests** — full container startup is too slow
- **`@MockBean` only in Spring slice tests** — use `@Mock` in pure unit tests
- **Date/Time** — don't rely on `new Date()`, inject Clock
- **Async code** — use Awaitility to wait for results
