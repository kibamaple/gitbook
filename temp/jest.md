# JEST
facebook出品的测试工具。集断言，mock,spy和coverage等一体。方便了测试统一管理，避免为了测试而随意引用三方组件，而导致代码及组件混乱的问题。
> 当前版本：22.4.3
### 内置
> afterAll(fn, timeout)
> afterEach(fn, timeout)
> beforeAll(fn, timeout)
> beforeEach(fn, timeout)
> describe(name, fn)
> describe.only(name, fn)
> describe.skip(name, fn)
> require.requireActual(moduleName)
> require.requireMock(moduleName)
> test(name, fn, timeout) 别名 it(name, fn, timeout)
> test.only(name, fn, timeout) 别名 it.only(name, fn, timeout)
> test.skip(name, fn) 别名 it.skip(name, fn)
### 断言
> expect(value)
> expect.extend(matchers)
> expect.anything()
> expect.any(constructor)
> expect.arrayContaining(array)
> expect.assertions(number)
> expect.hasAssertions()
> expect.objectContaining(object)
> expect.stringContaining(string)
> expect.stringMatching(regexp)
> expect.addSnapshotSerializer(serializer)
> .not
> .resolves
> .rejects
> .toBe(value)
> .toHaveBeenCalled()
> .toHaveBeenCalledTimes()
> .toHaveBeenCalledWith(arg1, arg2, ...)
> .toHaveBeenLastCalledWith(arg1, arg2, ...)
> .toBeCloseTo(number, numDigits)
> .toBeDefined()
> .toBeFalsy()
> .toBeGreaterThan(number)
> .toBeGreaterThanOrEqual(number)
> .toBeLessThan(number)
> .toBeLessThanOrEqual(number)
> .toBeInstanceOf(Class)
> .toBeNull()
> .toBeTruthy()
> .toBeUndefined()
> .toContain(item)
> .toContainEqual(item)
> .toEqual(value)
> .toHaveLength(number)
> .toMatch(regexpOrString)
> .toMatchObject(object)
> .toHaveProperty(keyPath, value)
> .toMatchSnapshot(optionalString)
> .toThrow(error)
> .toThrowErrorMatchingSnapshot()
### mock函数
> mockFn.getMockName()
> mockFn.mock.calls
> mockFn.mock.instances
> mockFn.mockClear()
> mockFn.mockReset()
> mockFn.mockRestore()
> mockFn.mockImplementation(fn)
> mockFn.mockImplementationOnce(fn)
> mockFn.mockName(value)
> mockFn.mockReturnThis()
> mockFn.mockReturnValue(value)
> mockFn.mockReturnValueOnce(value)
> mockFn.mockResolvedValue(value)
> mockFn.mockResolvedValueOnce(value)
> mockFn.mockRejectedValue(value)
> mockFn.mockRejectedValueOnce(value)
### Jest对象
> jest.clearAllTimers()
> jest.disableAutomock()
> jest.enableAutomock()
> jest.fn(implementation)
> jest.isMockFunction(fn)
> jest.genMockFromModule(moduleName)
> jest.mock(moduleName, factory, options)
> jest.unmock(moduleName)
> jest.doMock(moduleName, factory, options)
> jest.dontMock(moduleName)
> jest.clearAllMocks()
> jest.resetAllMocks()
> jest.restoreAllMocks()
> jest.resetModules()
> jest.runAllTicks()
> jest.runAllTimers()
> jest.advanceTimersByTime(msToRun)
> jest.runOnlyPendingTimers()
> jest.setMock(moduleName, moduleExports)
> jest.setTimeout(timeout)
> jest.useFakeTimers()
> jest.useRealTimers()
> jest.spyOn(object, methodName)
> jest.spyOn(object, methodName, accessType?)
### Jest配置
> automock [boolean]
> browser [boolean]
> bail [boolean]
> cacheDirectory [string]
> collectCoverage [boolean]
> collectCoverageFrom [array]
> coverageDirectory [string]
> coveragePathIgnorePatterns [array]
> coverageReporters [array]
> coverageThreshold [object]
> forceCoverageMatch [array]
> globals [object]
> globalSetup [string]
> globalTeardown [string]
> moduleFileExtensions [array]
> moduleDirectories [array]
> moduleNameMapper [object<string, string>]
> modulePathIgnorePatterns [array]
> modulePaths [array]
> notify [boolean]
> notifyMode [string]
> preset [string]
> projects [array<string | projectconfig>]
> clearMocks [boolean]
> reporters [array<modulename | [modulename, options]>]
> resetMocks [boolean]
> resetModules [boolean]
> resolver [string]
> restoreMocks [boolean]
> rootDir [string]
> roots [array]
> runner [string]
> setupFiles [array]
> setupTestFrameworkScriptFile [string]
> snapshotSerializers [array]
> testEnvironment [string]
> testEnvironmentOptions [Object]
> testMatch [array]
> testPathIgnorePatterns [array]
> testRegex [string]
> testResultsProcessor [string]
> testRunner [string]
> testURL [string]
> timers [string]
> transform [object<string, string>]
> transformIgnorePatterns [array]
> unmockedModulePathPatterns [array]
> verbose [boolean]
> watchPathIgnorePatterns [array]
### CLI命令
> jest <regexForTestFiles>
> --bail
> --cache
> --changedFilesWithAncestor
> --changedSince
> --ci
> --clearCache
> --collectCoverageFrom=<glob>
> --colors
> --config=<path>
> --coverage
> --debug
> --env=<environment>
> --expand
> --findRelatedTests <spaceSeparatedListOfSourceFiles>
> --forceExit
> --help
> --json
> --outputFile=<filename>
> --lastCommit
> --listTests
> --logHeapUsage
> --maxWorkers=<num>
> --noStackTrace
> --notify
> --onlyChanged
> --passWithNoTests
> --projects <project1> ... <projectN>
> --runInBand
> --setupTestFrameworkScriptFile=<file>
> --showConfig
> --silent
> --testNamePattern=<regex>
> --testLocationInResults
> --testPathPattern=<regex>
> --testRunner=<path>
> --updateSnapshot
> --useStderr
> --verbose
> --version
> --watch
> --watchAll
> --watchman
### 备注