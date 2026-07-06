Fix the test setup for the Logs Explorer page by implementing a custom render utility that includes necessary context providers and updating the global test setup with required mocks. Ensure that the tests correctly verify URL-driven behavior by removing manual mock overrides.

*   Upgrade the `nuqs` dependency in `apps/studio/package.json` to version `^2.4.1`.
*   Create `apps/studio/tests/lib/custom-render.tsx`:
    *   Export a function `customRender(component: React.ReactElement, renderOptions?: CustomRenderOpts) -> RenderResult`.
    *   Wrap the component in `QueryClientProvider`, `NuqsTestingAdapter`, and `TooltipProvider` in that order.
    *   Extend `CustomRenderOpts` from `RenderOptions` and include optional `queryClient` and `nuqs` fields.
    *   Create a new `QueryClient` instance if `queryClient` is not provided.
*   Update `apps/studio/tests/vitestSetup.ts`:
    *   Inside the `beforeAll` block, mock the `next/navigation` module:
        *   Spread the actual module using `vi.importActual('next/navigation')`.
        *   Override `useRouter` to return `{ push: vi.fn(), replace: vi.fn() }`.
        *   Override `usePathname` to return `vi.fn()`.
        *   Override `useSearchParams` to return `{ get: vi.fn() }`.
*   Modify `apps/studio/tests/pages/projects/logs-query.test.tsx`:
    *   Import `customRender as render` from `tests/lib/custom-render`.
    *   Remove the manual mock of the `nuqs` module that hardcoded `useQueryState`.
    *   Change the parameter name in `vi.doMock('common', ...)` to `importOriginal` and use it to retrieve the original module.
    *   Change `test('query warnings', ...)` to `test.skip('query warnings', ...)`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.