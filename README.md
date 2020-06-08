## Laboratory work V

[![Build Status](https://travis-ci.com/nastya-asya/lab05.svg?branch=master)](https://travis-ci.com/nastya-asya/lab05)

<a href="https://yandex.ru/efir/?stream_id=vQw_LH0UfN6I"><img src="https://raw.githubusercontent.com/tp-labs/lab05/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```sh
$ open https://github.com/google/googletest
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=nastya-asya
$ alias gsed=sed # for *-nix system
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab05
$ cd projects/lab05
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05
```

```sh
$ mkdir third-party
$ git submodule add https://github.com/google/googletest third-party/gtest
Клонирование в «/home/nastya-asya/nastya-asya/workspace/projects/lab05/third-party/gtest»…
remote: Enumerating objects: 20517, done.
remote: Total 20517 (delta 0), reused 0 (delta 0), pack-reused 20517
Получение объектов: 100% (20517/20517), 7.59 MiB | 5.10 MiB/s, готово.
Определение изменений: 100% (15174/15174), готово.
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../..
$ git add third-party/gtest
$ git commit -m"added gtest framework"
[master f99ce97] added gtest framework
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

```sh
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check \${PROJECT_NAME} gtest_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```

```sh
$ mkdir tests
$ cat > tests/test1.cpp <<EOF
#include <print.hpp>

#include <gtest/gtest.h>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};

  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;

  EXPECT_EQ(result, text);
}
EOF
```

```sh
$ cmake -H. -B_build -DBUILD_TESTS=ON
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PythonInterp: /usr/bin/python3.6 (found version "3.6.9") 
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - done
-- Check if compiler accepts -pthread
-- Check if compiler accepts -pthread - yes
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/nastya-asya/nastya-asya/workspace/projects/lab05/_build
$ cmake --build _build
Scanning dependencies of target print
[  8%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 16%] Linking CXX static library libprint.a
[ 16%] Built target print
Scanning dependencies of target gtest
[ 25%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 33%] Linking CXX static library libgtest.a
[ 33%] Built target gtest
Scanning dependencies of target gtest_main
[ 41%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library libgtest_main.a
[ 50%] Built target gtest_main
Scanning dependencies of target check
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
Scanning dependencies of target gmock
[ 75%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 83%] Linking CXX static library libgmock.a
[ 83%] Built target gmock
Scanning dependencies of target gmock_main
[ 91%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library libgmock_main.a
[100%] Built target gmock_main
$ cmake --build _build --target test
Running tests...
Test project /home/nastya-asya/nastya-asya/workspace/projects/lab05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.02 sec
```

```sh
$ _build/check
Running main() from /home/nastya-asya/nastya-asya/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from Print
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (1 ms)
[----------] 1 test from Print (1 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (1 ms total)
[  PASSED  ] 1 test.
$ cmake --build _build --target test -- ARGS=--verbose
Running tests...
UpdateCTestConfiguration  from :/home/nastya-asya/nastya-asya/workspace/projects/lab05/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/home/nastya-asya/nastya-asya/workspace/projects/lab05/_build/DartConfiguration.tcl
Test project /home/nastya-asya/nastya-asya/workspace/projects/lab05/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: check

1: Test command: /home/nastya-asya/nastya-asya/workspace/projects/lab05/_build/check
1: Test timeout computed to be: 10000000
1: Running main() from /home/nastya-asya/nastya-asya/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
1: [==========] Running 1 test from 1 test case.
1: [----------] Global test environment set-up.
1: [----------] 1 test from Print
1: [ RUN      ] Print.InFileStream
1: [       OK ] Print.InFileStream (0 ms)
1: [----------] 1 test from Print (0 ms total)
1: 
1: [----------] Global test environment tear-down
1: [==========] 1 test from 1 test case ran. (0 ms total)
1: [  PASSED  ] 1 test.
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

```sh
$ gsed -i 's/lab05/lab05/g' README.md
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml
$ gsed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```sh
$ travis lint
Hooray, .travis.yml looks valid :)
```

```sh
$ git add .travis.yml
$ git add tests
$ git add -p
$ git commit -m"added tests"
$ git push origin master
```

```sh
$ travis login --auto
Successfully logged in as nastya-asya!
$ travis enable
nastya-asya/lab05: enabled :)
```

```sh
$ mkdir artifacts
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png
# for macOS: $ screencapture -T 20 artifacts/screenshot.png
# open https://github.com/${GITHUB_USERNAME}/lab05
```

## Report

```sh
$ popd
$ export LAB_NUMBER=05
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

[![Build Status](https://travis-ci.com/nastya-asya/hw05.svg?branch=master)](https://travis-ci.com/nastya-asya/hw05) [![Coverage Status](https://coveralls.io/repos/github/nastya-asya/hw05/badge.svg?branch=master)](https://coveralls.io/github/nastya-asya/hw05?branch=master)

### Задание
1. Создайте `CMakeList.txt` для библиотеки *banking*.

```sh
$ git remote remove origin
$ hub create
$ git push -u origin master
```

Создаем CMakeLists.txt

```sh
$ cat >> CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.10)
project(banking)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(account STATIC banking/Account.cpp)
target_include_directories(account
 PUBLIC \${CMAKE_CURRENT_SOURCE_DIR}/banking)

add_library(transaction STATIC banking/Transaction.cpp)
target_include_directories(account
 PUBLIC \${CMAKE_CURRENT_SOURCE_DIR}/banking)
 target_link_libraries(transaction account)
EOF
```

Подключение к репозиторию подмодуля Google Test

```sh
$ mkdir third-party
$ git submodule add https://github.com/google/googletest third-party/gtest
Клонирование в «/home/nastya-asya/nastya-asya/workspace/projects/hw05/third-party/gtest»…
remote: Enumerating objects: 20517, done.
remote: Total 20517 (delta 0), reused 0 (delta 0), pack-reused 20517
Получение объектов: 100% (20517/20517), 7.59 MiB | 5.10 MiB/s, готово.
Определение изменений: 100% (15174/15174), готово.
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../..
$ git add third-party/gtest
$ git commit -m"added gtest framework"
[master f99ce97] added gtest framework
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

Модификация CMakeLists.txt

```sh
$ gsed -i "" '/set(CMAKE_CXX_STANDARD_REQUIRED ON)/a\
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt

$ cat >> CMakeLists.txt <<EOF
if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check account transaction gtest_main gmock_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```

2. Создайте модульные тесты на классы `Transaction` и `Account`.
    * Используйте mock-объекты.
    * Покрытие кода должно составлять 100%.

```sh 
$ cat >> tests/test1.cpp <<EOF
#include <Account.h>
#include <gtest/gtest.h>

TEST(Account, Constructor)
{
Account a(2,300);

EXPECT_EQ(a.id(),2);
EXPECT_EQ(a.GetBalance(),300);
}

TEST(Account, ChangeBalance)
{
  Account a(2,300);
  a.Lock();
  a.ChangeBalance(100);
  EXPECT_EQ(a.GetBalance(),400);
}

TEST(Account, Lock)
{
  Account a(2,300);
  a.Lock();
  a.ChangeBalance(100);
  a.Unlock();
  EXPECT_EQ(a.GetBalance(),400);
}
EOF
```

Создание тестов для класса Transaction

```sh
cat >> tests/test2.cpp <<EOF
#include <Account.h>
#include <Transaction.h>
#include <gmock/gmock.h>
#include <gtest/gtest.h>

class MockAccount : public Account {
public:
  MockAccount(){};
  MOCK_METHOD(int, GetBalance, (), (const, override));
  MOCK_METHOD(int, id, (), (const));
};

TEST(Transaction, MakeTransaction) {

  MockAccount from;
  MockAccount to;
  Transaction transaction1;

  EXPECT_CALL(from, id()).WillOnce(testing::Return(1));
  EXPECT_CALL(from, GetBalance()).WillOnce(testing::Return(1000));
  EXPECT_CALL(to, id()).WillOnce(testing::Return(2));
  EXPECT_CALL(to, GetBalance()).WillOnce(testing::Return(100));
  EXPECT_TRUE(transaction1.Make(Account(from.id(), from.GetBalance()),
                                Account(to.id(), to.GetBalance()), 500));
}

TEST(Transaction, Fee){
  Transaction transaction1;
  EXPECT_EQ(transaction1.fee(),1);
  transaction1.set_fee(3);
  EXPECT_EQ(transaction1.fee(),3);
  EXPECT_TRUE(transaction1.Make(Account(3, 1000),
                                Account(4,100), 500));
}
EOF
```
Сборка проекта

```sh
$ cmake -H. -B_build -DBUILD_TESTS=ON
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PythonInterp: /usr/bin/python3.6 (found version "3.6.9") 
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - done
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /home/nastya-asya/nastya-asya/workspace/projects/hw05/_build
$ cmake --build _build
Scanning dependencies of target transaction
[  6%] Building CXX object CMakeFiles/transaction.dir/banking/Transaction.cpp.o
[ 13%] Linking CXX static library libtransaction.a
[ 13%] Built target transaction
Scanning dependencies of target account
[ 20%] Building CXX object CMakeFiles/account.dir/banking/Account.cpp.o
[ 26%] Linking CXX static library libaccount.a
[ 26%] Built target account
[ 40%] Built target gtest
[ 53%] Built target gmock
[ 66%] Built target gmock_main
[ 80%] Built target gtest_main
Scanning dependencies of target check
[ 86%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 93%] Building CXX object CMakeFiles/check.dir/tests/test2.cpp.o
[100%] Linking CXX executable check
[100%] Built target check
$ cmake --build _build --target test
Running tests...
Test project /home/nastya-asya/nastya-asya/workspace/projects/hw05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.02 sec
```

Запуск тестов

```sh
$ _build/check
Running main() from /home/nastya-asya/nastya-asya/workspace/projects/hw05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 5 tests from 2 test suites.
[----------] Global test environment set-up.
[----------] 3 tests from Account
[ RUN      ] Account.Constructor
[       OK ] Account.Constructor (0 ms)
[ RUN      ] Account.ChangeBalance
[       OK ] Account.ChangeBalance (0 ms)
[ RUN      ] Account.Lock
[       OK ] Account.Lock (0 ms)
[----------] 3 tests from Account (0 ms total)

[----------] 2 tests from Transaction
[ RUN      ] Transaction.MakeTransaction
1 send to 2 $500
Balance 1 is 499
Balance 2 is 600
[       OK ] Transaction.MakeTransaction (0 ms)
[ RUN      ] Transaction.Fee
3 send to 4 $500
Balance 3 is 497
Balance 4 is 600
[       OK ] Transaction.Fee (0 ms)
[----------] 2 tests from Transaction (0 ms total)

[----------] Global test environment tear-down
[==========] 5 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 5 tests.
$ cmake --build _build --target test -- ARGS=--verbose
Running tests...
UpdateCTestConfiguration  from :/home/nastya-asya/nastya-asya/workspace/projects/hw05/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/home/nastya-asya/nastya-asya/workspace/projects/hw05/_build/DartConfiguration.tcl
Test project /home/nastya-asya/nastya-asya/workspace/projects/hw05/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: check

1: Test command: /home/nastya-asya/nastya-asya/workspace/projects/hw05/_build/check
1: Test timeout computed to be: 10000000
1: Running main() from /home/nastya-asya/nastya-asya/workspace/projects/hw05/third-party/gtest/googletest/src/gtest_main.cc
1: [==========] Running 5 tests from 2 test suites.
1: [----------] Global test environment set-up.
1: [----------] 3 tests from Account
1: [ RUN      ] Account.Constructor
1: [       OK ] Account.Constructor (0 ms)
1: [ RUN      ] Account.ChangeBalance
1: [       OK ] Account.ChangeBalance (0 ms)
1: [ RUN      ] Account.Lock
1: [       OK ] Account.Lock (0 ms)
1: [----------] 3 tests from Account (0 ms total)
1: 
1: [----------] 2 tests from Transaction
1: [ RUN      ] Transaction.MakeTransaction
1: 1 send to 2 $500
1: Balance 1 is 499
1: Balance 2 is 600
1: [       OK ] Transaction.MakeTransaction (0 ms)
1: [ RUN      ] Transaction.Fee
1: 3 send to 4 $500
1: Balance 3 is 497
1: Balance 4 is 600
1: [       OK ] Transaction.Fee (0 ms)
1: [----------] 2 tests from Transaction (0 ms total)
1: 
1: [----------] Global test environment tear-down
1: [==========] 5 tests from 2 test suites ran. (0 ms total)
1: [  PASSED  ] 5 tests.
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

3. Настройте сборочную процедуру на **TravisCI**.

```sh
$ cat > .travis.yml <<EOF
language: cpp

jobs:
  include:
  - name: "Link an test"
    script:
    - cmake -H. -B_build -DBUILD_TESTS=ON
    - cmake --build _build
    - cmake --build _build --target test
    - _build/check
    - cmake --build _build --target test -- ARGS=--verbose
addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```

Проверка .travis.yml на ошибки

```sh
travis lint
Hooray, .travis.yml looks valid :)
% travis login --auto
Successfully logged in as nastya-asya!
% travis enable
nastya-asya/hw05: enabled :)
```

4. Настройте [Coveralls.io](https://coveralls.io/).

Обновим CMakeLists.txt

```sh
sed -i "" '/add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})/a\
target_compile_options(check PRIVATE --coverage)
target_link_libraries(check PRIVATE account transaction gtest_main gmock_main  --coverage)
' CMakeLists.txt
```

Перепишем сборочную процедуру на TravisCI

```sh
$ cat > .travis.yml <<EOF
language: cpp

jobs:
  include:
  - name: "Link an test"
    script:
    - cmake -H. -B_build -DBUILD_TESTS=ON
    - cmake --build _build
    - cmake --build _build --target test
    - _build/check
    - cmake --build _build --target test -- ARGS=--verbose
  - name: "Coveralls.io"
    before_install:
    - pyenv rehash
    - pip install cpp-coveralls
    - pyenv rehash
    script:
    - cmake -H. -B_build -DBUILD_TESTS=ON
    - cmake --build _build
    - ./_build/check
    after_success:
    - coveralls --root . -E ".*gtest.*" -E ".*CMakeFiles.*"

addons:
  apt:
    packages:
      - cmake
      - cmake-data
EOF
```

Проверка .travis.yml на ошибки

```sh
$ travis lint
Hooray, .travis.yml looks valid :)
```


## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2020 The ISC Authors
```
