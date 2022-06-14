---
layout: post
title: "깃허브 블로그 좀 만들어보자… Jekyll Error"
date: 2022-06-12
author: Minjun Shin
categories: technical
tags:
---


# 깃허브 블로그 좀 만들어보자… Jekyll Error

---

나는 처음에 `Jekyll`이라는 신문물을 무시하고 그냥 깃헙 페이지에 푸시하고 커밋하면서 수정하면 되겠지~ 했다가 상당히 매운 맛을 몇시간 동안 보게 되었다.

Instruction 메뉴에 있는 내용들을 다시는 무시하지 말라….

Ruby는 그냥 파이썬 관련한 것이다라고 알고 있었는데 이게 지금부터 만든 블로그와 아주 큰 관계가 있었다.

그 와중에 에러도 정말 많이 겪었는데 아래와 같이 해결했다.

```tsx
You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory
```

**Solution** : [https://jojoldu.tistory.com/288](https://jojoldu.tistory.com/288)

```tsx
An error occurred while installing ffi (1.11.1), and Bundler cannot continue.
```

**Solution :**

```tsx
bundle update ffi
```

<br />

### 포스팅이 정상적으로 나오지 않는다!!

---

처음에 `_config.yml` 파일에 최상단에 이런 말이 있었는데 이게 무슨 말인지 잘 몰랐다. 왜냐하면 Archive부터가 뭘 말하는건지 잘 몰랐기 때문에 무시했었다..

```yaml
plugins:
  - jekyll-archives # Sorry, not GitHub pages friendly!
  - jekyll-sitemap
  - jekyll-paginate-v2
```

아니나 다를까 포스팅을 올려도 보이지 않고, 최신 시간으로 올리는 포스트는 다 안보이고 아주 대환장파티였다.

여기저기서 정보를 긁어모으다가 결국 위에서 말했던 대로 `jekyll-archives` 가 그냥 사용하기에는 깃헙페이지하고 friendly….. 프레엔들리하지 않단다… 그래서 포스팅이 정상적으로 안되는 것이였다.

이후 rake publish에서 계속 에러가 발생하였는데… 정확한 원인은 잘 모르겠지만, 

처음에 `GITHUB_REPONAME`을 변경하다가 `master` 브랜치로 푸시하도록 되어 있길래 `main` 브랜치로 임의로 바꿔놓았는데, 아마 `main` 브랜치로 푸시하는 권한을 계속 갖지 못하는 것 같았다.

`master` branch로 푸시하도록 설정을 다시 변경하니 진행이 되었다.

SSH 세팅은 글로벌하게 다 되어있는데 어째서…? rake는 처음 보는 것이라 좀 더 공부해봐야겠다.

블로그 레포 가장 최상단에 rakefile 이라는 이름으로 파일을 하나 생성하고 아래와 같이 넣은 후 publish를 진행하였다. 

`master` 브랜치에 푸시하는 것이기 때문에, 깃허브 페이지 셋팅에서도 master 브랜치를 가리키도록 바꾸었다.

```ruby
require "rubygems"
require "tmpdir"

require "bundler/setup"
require "jekyll"

# Change your GitHub reponame
# 자신의 GitName/GitHub Project 명
GITHUB_REPONAME = "MinjunShin/tech_blog"

desc "Generate blog files"
task :generate do
  Jekyll::Site.new(Jekyll.configuration({
    "source"      => ".",
    "destination" => "_site"
  })).process
end

desc "Generate and publish blog to gh-pages"
task :publish => [:generate] do
  Dir.mktmpdir do |tmp|
    cp_r "_site/.", tmp

    pwd = Dir.pwd
    Dir.chdir tmp

    system "git init"
    system "git add ."
    #system "git config --global user.email you@example.com"
    #system "git config --global user.name Your Name"
    message = "Site updated at #{Time.now.utc}"
    system "git commit -m #{message.inspect}"
    system "git remote add origin git@github.com:#{GITHUB_REPONAME}.git"
    system "git push origin master --force"

    Dir.chdir pwd
  end
end
```

<br />

### 저 알 수 없는 코드 블록은 어째서..
---

![Untitled](/tech_blog/assets/posts/20220612_github/Untitled6.png)

사진과 같이 Reference 밑에 알 수 없는 코드블럭이 자꾸 생겼는데… 온갖 방법을 다 써보다가 결국 _config.yml에 해당 내용을 전부 지우는 것으로 해결했다.

![description만 남기고 다 지워버리자.](/tech_blog/assets/posts/20220612_github/Untitled7.png)
*** description만 남기고 다 지워버리자.*