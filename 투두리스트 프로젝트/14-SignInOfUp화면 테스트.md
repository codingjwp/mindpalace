# SignInOfUp 화면 테스트
```typescript
import { test, expect, describe} from 'vitest';
import { render, renderHook, screen, waitFor, act } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { customRender, routerSign} from './test-helper';
import { useValidation } from '../hooks/useValidation';
import { ChangeEvent } from 'react';


describe('SignInOfUp Page Components', () => {
  test('navigate to sign up or sign in page when Link click', async () => {
    render(customRender(routerSign));
    const user = userEvent.setup();
    let header = screen.getByRole('heading', {level: 1});
    expect(header).toHaveTextContent('Sign Up');
 
    let linkSignIn = screen.getByRole('link', {name: /Sign In/i});
    user.click(linkSignIn);
    await waitFor(() =>expect(routerSign.state.location.pathname).toBe('/signin'));
 
    header = screen.getByRole('heading', {level: 1});
    expect(header).toHaveTextContent('Sign In');
 
    linkSignIn = screen.getByRole('link', {name: /Sign Up/i});
    user.click(linkSignIn);
    await waitFor(() =>expect(routerSign.state.location.pathname).toBe('/signup'));
 });

 test('useValidation hook to see if the return value changes', () => {
  render(customRender(routerSign));
  const { result } = renderHook(() => useValidation('Sign Up'));
  expect(result.current[0]).toBe(false);
  expect(result.current[1]).toBe(false);
  expect(result.current[2]).toBe(true); 
  const button = screen.getByLabelText(/signinofup-btn/i);
  expect(button).toBeDisabled();

  act(() => {
    const email: ChangeEvent<HTMLInputElement> = {currentTarget: {value: '1t1t@test.com'}} as ChangeEvent<HTMLInputElement>; 
    const password: ChangeEvent<HTMLInputElement> = {currentTarget: {value: '123123123'}} as ChangeEvent<HTMLInputElement>; 
    result.current[3](email);
    result.current[4](password);
  })

  expect(result.current[0]).toBe(true);
  expect(result.current[1]).toBe(true);
  expect(result.current[2]).toBe(false); 
  }); 
});
```