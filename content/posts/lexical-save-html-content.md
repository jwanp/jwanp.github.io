---
title: '[React] Lexical 에더터에서 HTML 저장하는 두가지 방법'
date: 2025-06-12T14:39:34+09:00
draft: false
tags: ['javascript', 'react', 'lexical']
categories: ['Tech']
featuredImage: /images/tech/lexical.webp
---

[약알고](https://yakalgo.kr) 프로젝트에서는 글 꾸밈 기능을 구현할 때 Meta에서 관리 중인 오픈소스 라이브러리인 **[Lexical](https://lexical.dev/) 에디터**를 활용하였습니다. **React Lexical 에디터에서 내용을 HTML로 저장**하는 가장 대표적인 구현 방법은 저장 버튼을 플러그인으로 두는 것입니다. 하지만 **버튼 플러그인으로 개발하면 몇 가지 단점**이 있습니다.

## 버튼 플러그인 방식의 단점

### 1. **해당 버튼의 동작이 특정되어 확장성에 좋지 않음**

동일한 에디터를 가져다 쓰되, 저장 버튼의 동작 방식을 다르게 하고 싶을 때가 있습니다. 약알고 서비스도 QnA와 게시물 두 곳에서 같은 에디터를 사용하지만, 두 개의 '저장' 버튼은 다르게 동작해야 합니다. 이 경우 버튼만 다른 두 개의 에디터를 만들어야 하므로 코드 중복이 발생하고, 에디터 자체의 확장성이 떨어집니다.

### 2. **UI가 자연스럽지 않고 조작하기 번거로움**

보통 저장 버튼은 취소 버튼 옆에 위치하거나, 에디터와 독립적인 UI로 구성합니다. '약알고' 게시물 작성 UI도 제목과 내용을 입력하는 섹션과, 취소/저장 버튼이 있는 섹션이 별도로 구분되어 보여집니다. 따라서 에디터 안에서 버튼 UI를 구성하려면 더 까다로운 과정을 거쳐야 합니다.

## 그래서, 버튼을 플러그인으로 두지 않고 HTML로 변환하여 저장

저는 버튼을 에디터의 플러그인으로 두지 않고, 버튼을 별개의 로직으로 분리하여 HTML로 변환한 뒤 저장하였습니다.

이 방식은 크게 두 가지로 구현할 수 있습니다.

1. **HTML 변환 플러그인을 통해 글 수정 시마다 HTML string 받아오기**
2. **`createEditor`에 `theme`를 주입하여, 새로운 에디터에서 HTML string 받아오기**

- 첫 번째 방법은 에디터를 수정할 때마다 실시간으로 HTML을 받아올 수 있다는 장점이 있습니다.
- 두 번째 방법은 추가 렌더링이나 연산 없이, 저장 버튼을 누른 시점에만 HTML 연산을 수행하면 된다는 장점이 있습니다.

각 방법의 장점은 서로의 단점이기도 합니다. 저도 처음에는 첫 번째 방법을 사용했습니다.

그러나 '저장할 때만 HTML이 필요한데 더 효율적인 방법은 없을까?' 라고 생각하여 두번째 방법을 고안하게 되었습니다.

**html가져오는 방법 간단 요약**

- `import { $generateHtmlFromNodes } from "@lexical/html";`
- `$generateHtmlFromNodes`에 에디터를 주입하여 html을 가져올 수 있습니다.
  - 에디터를 어떻게 가져올 것인가?
  1.  플러그인으로 삽입하여 에디터 주입
  2.  새로운 에디터 생성하여 에디터 주입

## 1. 플러그인을 활용해서 에디터의 HTML 저장하기

React에서 Lexical을 사용할 때 HTML 변환 플러그인을 만들지 않고 HTML로 변환하는 방법은 공식 문서에 나와 있지 않습니다.

> ⚠️ `LexicalComposer` 내부에서만 `useLexicalComposerContext`를 통해 `editor` 인스턴스를 가져올 수 있습니다. 외부에서 호출 시 에러가 발생합니다.

### 에디터 구현 예시

```tsx
// MyEditor.tsx

import React from 'react';
import { LexicalComposer, ContentEditable, RichTextPlugin, HistoryPlugin, OnChangePlugin } from '@lexical/react';
import { LexicalErrorBoundary } from '@lexical/react/LexicalErrorBoundary';
import { useLexicalComposerContext } from '@lexical/react/LexicalComposerContext';
import { $generateHtmlFromNodes } from '@lexical/html';

const theme = {
  // 원하는 스타일 적용
};

const editorConfig = {
  theme,
  onError(error: Error) {
    console.error('Lexical Error:', error);
  },
  namespace: 'MyEditor',
  nodes: [], // 필요한 노드 추가
};

// 글 내용을 html으로 실시간 변환하는 플러그인
function ExportHtmlPlugin({ onHtmlChange }: { onHtmlChange: (html: string) => void }) {
  const [editor] = useLexicalComposerContext();

  React.useEffect(() => {
    return editor.registerUpdateListener(({ editorState }) => {
      editorState.read(() => {
        const htmlString = $generateHtmlFromNodes(editor, null);
        onHtmlChange(htmlString);
      });
    });
  }, [editor]);

  return null;
}

// 에디터 컴포넌트: 플러그인들을 넣어서 사용한다.
export default function MyEditor({ onHtmlChange }: { onHtmlChange: (html: string) => void }) {
  return (
    <LexicalComposer initialConfig={editorConfig}>
      <RichTextPlugin
        contentEditable={<ContentEditable className="editor-input" />}
        placeholder={<div className="editor-placeholder">텍스트를 입력하세요...</div>}
        ErrorBoundary={LexicalErrorBoundary}
      />
      <HistoryPlugin />
      <ExportHtmlPlugin onHtmlChange={onHtmlChange} /> // html 실시간 변환
    </LexicalComposer>
  );
}
```

### 사용 예시

```jsx
import React, { useState } from 'react';
import MyEditor from './MyEditor';

export default function EditorPage() {
  const [html, setHtml] = useState(''); // 여기에 글의 html이 실시간으로 수정됩니다.

  return (
    <div>
      <h1>Lexical 에디터</h1>
      <MyEditor onHtmlChange={setHtml} />
      <h2>HTML Output</h2>
      <pre>{html}</pre>
    </div>
  );
}
```

## 2. `createEditor`로 새로운 에디터에서 HTML 불러오기

약알고에서는 DB에 에디터의 HTML뿐만 아니라 `editorState`도 JSON 형태로 저장하고 있습니다. 이를 위해 `onChangePlugin`을 통해 `useRef`에 상태를 저장하고, 이후 저장시 `editorState`를 HTML로 변환합니다.

> 이 방식은 불필요한 플러그인 없이 `editor => HTML` 변환만 수행하면 되므로 `lexical/react`를 사용하지 않고 `lexical`만 사용합니다.

### HTML 변환 함수

```jsx
import { createEditor, SerializedEditorState } from 'lexical';
import { $generateHtmlFromNodes } from '@lexical/html';
import { nodes } from '@/components/blocks/editor-x/nodes';
import { editorTheme } from '@/components/editor/themes/editor-theme';

export const getEditorHtmlFromJSON = (json: SerializedEditorState) => {
  const editor = createEditor({
    nodes,
    theme: editorTheme,
  });

  try {
    editor.setEditorState(editor.parseEditorState(json));
    let html = '';
    editor.update(() => {
      html = $generateHtmlFromNodes(editor);
    });
    return html;
  } catch (error) {
    console.error(error);
    return '';
  }
};
```

### 저장 버튼과 연결된 에디터 구현 예시

```jsx
'use client';

import { useRef } from 'react';
import dynamic from 'next/dynamic';
import { Button } from '@/components/ui/button';
import { SerializedEditorState } from 'lexical';
import { getEditorHtmlFromJSON } from '@/lib/community/getEditorHtmlFromJSON';

const Editor = dynamic(() => import('@/components/blocks/editor-y/editor').then((mod) => mod.Editor), {
  ssr: false,
  loading: () => <div className="h-72 w-full animate-pulse rounded-lg bg-muted" />,
});

export default function EditorWithSaveButton() {
  const editorState = (useRef < SerializedEditorState) | (null > null);

  const handleSave = () => {
    if (!editorState.current) {
      alert('에디터 내용이 비어있습니다.');
      return;
    }

    const html = getEditorHtmlFromJSON(editorState.current);
    console.log('HTML content:', html);
    // 서버에 전송하거나 미리보기 등
  };

  return (
    <div className="flex flex-col gap-4 max-w-3xl mx-auto py-8">
      <Editor editorSerializedState={null} onSerializedChange={(value) => (editorState.current = value)} />
      <div className="flex justify-end">
        <Button onClick={handleSave}>저장</Button>
      </div>
    </div>
  );
}
```
