# 01｜Todo（Web）：Next.js + shadcn/ui + Tailwind + react-query + axios

这一章我会带你：给出一个“你熟悉的前端工程化 Todo”蓝图，并明确它和 Flutter 版本在需求、接口、UI 结构上的对应关系。

## 0) 技术栈（你关心的都在）

- Next.js（App Router）+ TypeScript
- TailwindCSS（布局/响应式）+ shadcn/ui（组件体系）+（可选）SCSS/Emotion
- TanStack Query（react-query）做 server state
- axios 作为 HTTP client
- nanoid 生成 id
- Biome 作为 format/lint
-（可选）next-intl 做 i18n、next-themes 做主题

## 1) 需求与数据模型（与 Flutter 版本保持一致）

### 功能

- 添加 / 切换完成 / 删除
- 筛选：all / active / done
- 响应式：<640 单列；>=640 改变布局密度（或双列 master-detail）
- 主题：浅色/深色/系统
- 多语言：en/zh（至少覆盖按钮/空状态/错误提示）

### 类型（对齐接口契约）

```ts
export type Todo = {
  id: string;
  title: string;
  done: boolean;
  createdAt: string; // ISO UTC
};
```

## 2) 推荐目录结构（App Router）

```text
src/
  app/
    layout.tsx                  # Providers：Theme + Query + i18n
    page.tsx                    # Todo 页面
    api/
      todos/route.ts            # GET/POST
      todos/[id]/route.ts       # PATCH/DELETE
  components/todos/
    TodoForm.tsx
    TodoToolbar.tsx             # filter + theme + locale（可选）
    TodoList.tsx
    TodoRow.tsx
  lib/
    api.ts                      # axios instance
    todoStore.ts                # demo：进程内存储（换 DB/服务即可）
    todos.ts                    # request functions
    types.ts
```

## 3) API（Route Handlers，demo 版）

> 说明：进程内存储只用于演示。真实项目请换 DB/外部服务。

`src/lib/todoStore.ts`（示意）：

```ts
import { nanoid } from "nanoid";
import type { Todo } from "./types";

let todos: Todo[] = [];

export function list(): Todo[] {
  return todos;
}

export function create(title: string): Todo {
  const todo: Todo = { id: nanoid(), title, done: false, createdAt: new Date().toISOString() };
  todos = [todo, ...todos];
  return todo;
}

export function toggle(id: string): Todo | null {
  const idx = todos.findIndex((t) => t.id === id);
  if (idx < 0) return null;
  const updated = { ...todos[idx], done: !todos[idx].done };
  todos = [...todos.slice(0, idx), updated, ...todos.slice(idx + 1)];
  return updated;
}

export function remove(id: string): boolean {
  const next = todos.filter((t) => t.id !== id);
  const changed = next.length !== todos.length;
  todos = next;
  return changed;
}
```

`src/app/api/todos/route.ts`（示意）：

```ts
import { NextResponse } from "next/server";
import { create, list } from "@/lib/todoStore";

export async function GET() {
  return NextResponse.json(list());
}

export async function POST(req: Request) {
  const body = (await req.json()) as { title?: unknown };
  if (typeof body.title !== "string" || body.title.trim().length === 0) {
    return NextResponse.json({ message: "Invalid title" }, { status: 400 });
  }
  return NextResponse.json(create(body.title.trim()), { status: 201 });
}
```

`src/app/api/todos/[id]/route.ts`（示意）：

```ts
import { NextResponse } from "next/server";
import { remove, toggle } from "@/lib/todoStore";

export async function PATCH(_: Request, ctx: { params: { id: string } }) {
  const updated = toggle(ctx.params.id);
  if (!updated) return NextResponse.json({ message: "Not found" }, { status: 404 });
  return NextResponse.json(updated);
}

export async function DELETE(_: Request, ctx: { params: { id: string } }) {
  const ok = remove(ctx.params.id);
  if (!ok) return NextResponse.json({ message: "Not found" }, { status: 404 });
  return NextResponse.json({ ok: true });
}
```

## 4) 请求层：axios（对位 dio）

`src/lib/api.ts`：

```ts
import axios from "axios";
export const api = axios.create({ baseURL: "/api" });
```

`src/lib/todos.ts`：

```ts
import { api } from "./api";
import type { Todo } from "./types";

export async function listTodos() {
  const { data } = await api.get<Todo[]>("/todos");
  return data;
}

export async function createTodo(title: string) {
  const { data } = await api.post<Todo>("/todos", { title });
  return data;
}

export async function toggleTodo(id: string) {
  const { data } = await api.patch<Todo>(`/todos/${id}`);
  return data;
}

export async function deleteTodo(id: string) {
  await api.delete(`/todos/${id}`);
}
```

## 5) server state：react-query（对位 Riverpod AsyncNotifier）

```tsx
import { useMutation, useQuery, useQueryClient } from "@tanstack/react-query";
import { createTodo, deleteTodo, listTodos, toggleTodo } from "@/lib/todos";
import type { Todo } from "@/lib/types";

export function useTodos() {
  return useQuery({ queryKey: ["todos"], queryFn: listTodos });
}

export function useCreateTodo() {
  const qc = useQueryClient();
  return useMutation({
    mutationFn: createTodo,
    onSuccess: () => qc.invalidateQueries({ queryKey: ["todos"] }),
  });
}

export function useToggleTodo() {
  const qc = useQueryClient();
  return useMutation({
    mutationFn: toggleTodo,
    onMutate: async (id: string) => {
      await qc.cancelQueries({ queryKey: ["todos"] });
      const prev = qc.getQueryData<Todo[]>(["todos"]);
      qc.setQueryData<Todo[]>(["todos"], (old) =>
        old?.map((t) => (t.id === id ? { ...t, done: !t.done } : t)),
      );
      return { prev };
    },
    onError: (_err, _id, ctx) => {
      if (ctx?.prev) qc.setQueryData(["todos"], ctx.prev);
    },
    onSettled: () => qc.invalidateQueries({ queryKey: ["todos"] }),
  });
}
```

## 6) UI：Tailwind + shadcn（布局/响应式/主题/i18n）

我不强制你在这里把完整页面一次写完，但我建议你把 UI 拆成：

- `TodoForm`：输入 + 添加按钮
- `TodoToolbar`：筛选 + 主题切换 + 语言切换
- `TodoList/TodoRow`：列表与行

响应式：

- 以 `sm=640px` 为第一断点，调整 padding/字号/布局密度

主题：

- 用 `next-themes` + shadcn 的 CSS variables

i18n：

- 用 `next-intl` 或简化版 messages

## 7) 下一步（从 demo 到真实项目）

- 用 DB 替换 `todoStore`（Prisma/Drizzle）
- 输入校验用 Zod（或同类）补齐运行时安全
- 加测试：  
  - hooks/纯逻辑：Vitest  
  - 组件交互：RTL  
  - e2e：Playwright  
