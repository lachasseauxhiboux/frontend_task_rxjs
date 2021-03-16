# Задания Фронтенд

## Оператор LRU

Написать оператор RxJs и функционалом LRU (last recently used) кэширования.

Примерные интерфейсы для оператора:

```typescript
export interface LruItem<T> {
    // Закэшированное значение
    value: T;
    // Дата последнего образения к значению
    lastUsed: Date;
}

export interface LruStore<T> {
    [key: string]: LruItem<T>;
}

export interface Lru<T> {
    store: LruStore<T>;
    // Максимальное количество объектов в кэше
    readonly bucketSize: number;

    has(id: string): boolean;

    get(id: string): LruItem<T> | undefined;

    getValue(id: string): T | undefined;

    set(id: string, value: T): void;

    delete(id: string): void;
}
```

Так же есть функция и источник данных:

```typescript
// Функция, которая получает объект
export interface Get<T> {
    (id: string): Observable<T>;
}

// LRU оператор функция
export interface LruCacheFn<R> {
    (project: Get<R>): OperatorFunction<string, R>;
}

// Источник данных (для упрощения обычный BehaviorSubject)
export const subject = new BehaviorSubject<Record<string, Person>>({
    '1': {
        id: 1,
        name: 'First'
    },
    '2': {
        id: 2,
        name: 'Second'
    },
    '3': {
        id: 3,
        name: 'Third'
    },
    '4': {
        id: 4,
        name: 'Fourth'
    }
});

// Функция для получения данных
export function get(id: string): Observable<Person | undefined> {
    return subject.pipe(map(x => x[id]))
}
```

При использовании оператора c bucketSize = 2 будет следующий вывод:

```typescript
of('1', '1', '2', '2', '3', '1').pipe(lru(get)).subscribe(console.log);
// Вывод 
// { id: 1, name: 'First' }
// Returning from cache
// { id: 1, name: 'First' }
// { id: 2, name: 'Second' }
// Returning from cache
// { id: 2, name: 'Second' }
// { id: 3, name: 'Second' }
// { id: 1, name: 'Second' }
```

Выполнение задания осуществляется в файле lru.ts. 
Чтобы проверить выполнение задания, нужно скомпилировать его при помощи команды `npm run build` 
и запустить при помощи команды `npm run lru`

Для выполнения код из этого репозитория заливается в свой репозиторий. 
Главное не делать fork, чтобы другие кандидаты не могли его увидеть! 
После выполнения через каналы связи направляется ссылка на репозиторий.
