strictNullChecks 설정을 켜두어야 null과 관련된 문제를 해결할 수 있음.

null 가능성을 타입으로 표현할 수 없음 → B변수가 A의 하위집합이라면, A의 null 여부를 따라감.

(A가 null이 될 수 없으면 B도 불가능하고, A가 null이 될 수 있으면 B도 가능함.) ⇒ 하지만 사람/타입체커 모두 확인할 수 없음.

```tsx
function extent(nums: number[]) {
  let min, max;
  for (const num of nums) {
    if (!min) {
      min = num;
      max = num;
    } else {
      min = Math.min(min, num);
      max = Math.max(max, num);
    }
  }
  return [min, max];
}
```

1. 최솟값이나 최댓값이 0일 경우 덮힘.
2. nums 배열이 비어있으면 for문이 실행되지 않으므로 undefined 배열이 반환됨.

⇒ min과 max의 상태가 동기화되어 있지 않으므로 문제가 발생함. ⇒ 따라서 둘을 한 객체에 넣기로 함!

⇒ 이렇게 해야 모든 케이스를 검사할 수 있고, 둘 다 null인 경우를 명시적으로 배제하는 케이스를 따로 처리함.

(`!`) 단언을 하거나, if문으로 체크할 수 있음.

- 클래스에서의 null 값 사용
  - 클래스에서는 null과 null이 아닌 값을 혼용하면 문제가 발생함. → 네트워크 요청을 통해 객체가 만들어 질 경우, 값의 개수에 따라 null 케이스가 다양해짐.
  ```tsx
  class UserPosts {
    user: UserInfo | null;
    posts: Post[] | null;
    constructor() {
      this.user = null;
      this.posts = null;
    }
    async init(userId: string) {
      return Promise.all([
        async () => this.user = await fetchUser(userId),
        async () => this.posts = await fetchPostsForUser(userId);
      ]);
    }
    ...
  }
  ```
  this를 통해 바로 값에 넣어버렸기 때문에, 둘 중 하나가 null인 상태가 존재함.
  ```tsx
  class UserPosts {
    user: UserInfo;
    posts: Post[];
    constructor(user: UserInfo, posts: Post[]) {
      this.user = user;
      this.posts = posts;
    }
    static async init(userId: string): Promise<UserPosts> {
      const [user, posts] = await Promise.all([
        fetchUser(userId),
        fetchPostsForUser(userId);
      ]);
      return new UserPosts(user, posts);
    }
    getUserName(){
      return this.user.name;
    }
  }
  ```
  둘 다 null이거나 둘 다 null이 아니거나 할 수 있음. → 예외가 생기더라도 처리가 용이함. (클래스는 null이 아니도록 모든 값이 있을 때 생성하도록 설계하는게 맞긴함.)

> 한 값의 null 여부가 다른 값의 null 여부에 암시적으로 관련되도록 설계하면 안됨.
