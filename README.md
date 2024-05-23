# ethers

> https://docs.ethers.org/v6/

# template clone

> git clone https://github.com/h662/vite-tailwind-template.git .

## 240522

> npm install ethers

- window.ehtereum?

```javascript
const App = () => {
  const onClickMetamask = async () => {
    try {
      console.log(window.ethereum);
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <div className="bg-red-100 min-h-screen flex justify-center items-center">
      <button onClick={onClickMetamask}>🦊 메타마스크 로그인</button>
    </div>
  );
};

export default App;
```

브라우저에 Metamask 지갑이 설치되어 있다면, Proxy(Object) 콘솔 값을 확인 할 수 있습니다.  
만약 설치되어 있지 않다면(스크릿 모드) undefined 값을 확인 할 수 있습니다.

```javascript
import { ethers } from "ethers";

const App = () => {
  const onClickMetamask = async () => {
    try {
      if (!window.ethereum) return;

      const provider = new ethers.BrowserProvider(window.ethereum);

      console.log(provider);

      const signer = await provider.getSigner();

      console.log(signer);
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <div className="bg-red-100 min-h-screen flex justify-center items-center">
      <button onClick={onClickMetamask}>🦊 메타마스크 로그인</button>
    </div>
  );
};

export default App;
```

ethers 임포트 후 onClickMetamask를 클릭하면 메타마스크 연결 창이 팝업됩니다.

연결 후 signer를 확인해보면, 아래와 같이 연결된 지갑 주소(address)를 확인 할 수 있습니다.

```javascript
JsonRpcSigner {provider: BrowserProvider, address: '연결된 지갑 주소'}
address : "연결된 지갑 주소"
provider : BrowserProvider {}
[[Prototype]] :  AbstractSigner
```

- button styling

간단하게 버튼/hover 스타일링해봅시다!

```css
/* index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .button-style {
    @apply border-4 border-green-300 rounded-full bg-white text-xl px-6 py-3 font-semibold hover:bg-gray-100 hover:border-green-400;
  }
}
```

```javascript
// App.jsx

import { ethers } from "ethers";
import { useState } from "react";

const App = () => {
  const [signer, setSigner] = useState();

  const onClickMetamask = async () => {
    try {
      if (!window.ethereum) return;

      const provider = new ethers.BrowserProvider(window.ethereum);

      console.log(provider);

      setSigner(await provider.getSigner());

      console.log(signer);
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <div className="bg-red-100 min-h-screen flex justify-center items-center">
      {signer ? (
        <div>{signer.address}</div>
      ) : (
        <button className="button-style" onClick={onClickMetamask}>
          🦊 메타마스크 로그인
        </button>
      )}
    </div>
  );
};

export default App;
```

signer의 값 중 address를 useState 사용해서 표현해봅시다.

signer가 있다면 signer.address(지갑주소)가 나타나고 없으면 메타마스크 로그인 버튼이 표현됩니다.

adress를 감싸고 있는 `<div>`태그도 스타일링 해봅시다.

```css
/* index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .button-style {
    @apply border-4 border-green-300 rounded-full bg-white text-xl px-6 py-3 font-semibold hover:bg-gray-100 hover:border-green-400;
  }

  .box-style {
    @apply bg-white rounded-xl text-xl font-semibold py-3 px-6;
  }
}
```

주소도 substring을 적용해봅시다.

```javascript
// App.jsx

import { ethers } from "ethers";
import { useState } from "react";

const App = () => {
  const [signer, setSigner] = useState();

  const onClickMetamask = async () => {
    try {
      if (!window.ethereum) return;

      const provider = new ethers.BrowserProvider(window.ethereum);

      setSigner(await provider.getSigner());
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <div className="bg-red-100 min-h-screen flex justify-center items-center">
      {signer ? (
        <div className="box-style">
          안녕하세요, {signer.address.substring(0, 5)}...
          {signer.address.substring(signer.address.length - 5)}
        </div>
      ) : (
        <button className="button-style" onClick={onClickMetamask}>
          🦊 메타마스크 로그인
        </button>
      )}
    </div>
  );
};

export default App;
```

이제 로그아웃 버튼을 만들어봅시다!

```javascript
// App.jsx

import { ethers } from "ethers";
import { useState } from "react";

const App = () => {
  const [signer, setSigner] = useState();

  const onClickMetamask = async () => {
    try {
      if (!window.ethereum) return;

      const provider = new ethers.BrowserProvider(window.ethereum);

      setSigner(await provider.getSigner());
    } catch (error) {
      console.error(error);
    }
  };

  const onClickLogOut = () => {};

  return (
    <div className="bg-red-100 min-h-screen flex justify-center items-center">
      {signer ? (
        <div className="flex gap-8">
          <div className="box-style">
            안녕하세요, {signer.address.substring(0, 7)}...
            {signer.address.substring(signer.address.length - 5)}님
          </div>
          <button
            className="button-style border-red-300 hover:border-red-400"
            onClick={onClickLogOut}
          >
            로그아웃
          </button>
        </div>
      ) : (
        <button className="button-style" onClick={onClickMetamask}>
          🦊 메타마스크 로그인
        </button>
      )}
    </div>
  );
};

export default App;
```

그럼 이제 onClickLogOut을 구현해봅시다.

```javascript
// App.jsx

import { ethers } from "ethers";
import { useState } from "react";

const App = () => {
  const [signer, setSigner] = useState();

  const onClickMetamask = async () => {
    try {
      if (!window.ethereum) return;

      const provider = new ethers.BrowserProvider(window.ethereum);

      setSigner(await provider.getSigner());
    } catch (error) {
      console.error(error);
    }
  };

  const onClickLogOut = () => {
    setSigner(null);
  };

  return (
    <div className="bg-red-100 min-h-screen flex justify-center items-center">
      {signer ? (
        <div className="flex gap-8">
          <div className="box-style">
            안녕하세요, {signer.address.substring(0, 7)}...
            {signer.address.substring(signer.address.length - 5)}님
          </div>
          <button
            className="button-style border-red-300 hover:border-red-400"
            onClick={onClickLogOut}
          >
            로그아웃
          </button>
        </div>
      ) : (
        <button className="button-style" onClick={onClickMetamask}>
          🦊 메타마스크 로그인
        </button>
      )}
    </div>
  );
};

export default App;
```

위 처럼 setSigner(null) 값을 주면 로그아웃 되는 것 처럼 보이지만, 메타마스크에서 직접 연결 해제하는 것과는 다르게 작동합니다.

### contracts

예제로 사용 할 스마트 컨트랙트를 만들어 봅시다.

위 순서대로 git clone을 하셨다면, 폴더 구조를 아래와 같이 변경해주세요.

FIRST-DAPP
-- contracts
-- vite (메타마스크 로그인 코드를 옮겨주세요)

### remix 연결하기

> remixd -s . --remix-ide https://remix.ethereum.org

> https://remix.ethereum.org/ 접속해서 connect to localhost를 해주세요.

<img
  src="vite/public/images/remixLocal.png"
  width="718"
  height="404"
  alt="connect local"
/>

contracts폴더에 MintToken.sol 파일을 생성해주세요.

```solidity
// MintToken.sol

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import { ERC20 } from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MintToken is ERC20 {
    constructor(uint256 _initEther, string memory _name, string memory _symbol) ERC20(_name, _symbol) {
        _mint(msg.sender, _initEther * 10 ** 18);
    }
}
```

위 코드를 배포해 봅시다!

‼️ 배포 전 컴파일 부터 실행해야 합니다! Auto compile 체크가 안되어 있다면 아래 사진을 보시고 확인해주세요!

<img
  src="vite/public/images/remixCompile.png"
  width="718"
  alt="auto compile check"
  sizes="100vw"
/>

배포하려면 metamask 지갑을 로그인해야 합니다.

<img
  src="vite/public/images/remixMetamask.png"
  width="718"
  alt="connect metamask"
  sizes="100vw"
/>

지갑 연결 후, CONTRACT 항목에 MintToken 파일이 잘 선택되었는지 확인하고 배포(DEPLOY)합니다.

배포 할 때, MintToken.sol 코드를 보면 constructor가 있습니다.

constructor는 스마트 컨트랙트가 배포 될 때, 최초 1번만 실행합니다.

\_initEther, \_name, \_symbol 값을 입력 후 deploy 하시면 배포됩니다!

<img
  src="vite/public/images/remixDeploy.png"
  width="718"
  alt="deploy"
  sizes="100vw"
/>

## 240523

### Git

현재 폴더 구조는 아래와 같습니다.

최상위 폴더

- ethers(FIRST-DAPP)

하위 폴더

- contracts
- vite

vite폴더는 git clone을 했기 때문에, 깃을 제거해줍니다. git remote remove origin 명령어가 아닌, 깃을 삭제해줍니다.

⚠️ vite 폴더 경로에서 아래 명령어를 순서대로 실행해주세요.

> rm -rf .git

깃 삭제 후 상위 폴더 ethers(FIRST-DAPP)으로 이동 후 아래와 같이 명령어를 실행합니다.

상위폴더로 이동 명령어

> cd ..

하위폴더로 이동 명령어

> cd 폴더명

깃 시작

> git init

git init명령어를 시작 후 .gitignore에 아래 폴더들을 추가로 작성해줍니다.

```javascript
.env
.deps
artifacts
```

각 폴더 및 폴더의 의미는 뭘까요?

- .env 파일은 환경 변수를 정의하고 관리하기 위한 파일입니다. 보통 루트 디렉토리에 위치합니다.

- .dpes 폴더는 "dependencies"의 약자로, 의존성 관련 파일들을 포함하고 있습니다. 폴더를 살펴보면 경로가 .dpes/npm/@openzeppelin/.. 으로 되어 있습니다. 한 가지 확인 할 수 있는 것은 MintToken.sol 코드 작성시 ERC20 을 import 했었죠? .deps/token/ERC20/extensions에 보면 ERC20.sol 파일도 볼 수 있습니다.

- artifacts 폴더는 MintToken.sol 파일의 컴파일 된 결과물들이 저장된 폴더입니다.

이제 깃에 업로드해줍니다.

커맨드 명령어는 아래의 순서와 같습니다.

> git add .  
> git commit -m "first commit"  
> git branch -M main  
> git remote add origin 연결할 깃 레포주소  
> git push -u origin main

### Vercel 배포하기

먼저 vercel에 배포해봅시다!

> https://vercel.com/

vercel에 접속하셔서 우측에 Add New - project를 선택합니다.

<img
  src="vite/public/images/vercel1.png"
  width="718"
  alt="vercel add new"
  sizes="100vw"
/>

ethers폴더를 선택해 줍니다. (또는 FIRST-DAPP으로 실습하신 분은 FIRST-DAPP 폴더명을 선택)

근데 중요한 점은, 최상위 폴더 FIRST-DAPP에서 하위 폴더로 vite와 contracts가 있습니다.

contracts는 굳이 배포 할 필요가 없기 때문에, vite폴더만 선택해서 배포합니다.

<img
  src="vite/public/images/vercel3.png"
  width="718"
  alt="Edit root directory"
  sizes="100vw"
/>

<img
  src="vite/public/images/vercel4.png"
  width="718"
  alt="select root directory vite"
  sizes="100vw"
/>

선택 후, 배포(Deploy)하면 vite폴더만 배포가 됩니다.

배포가 완료되었다면 주소로 접속해서 메타마스크 로그인 기능이 잘 작동되는지 확인해보세요!

### burn function

토큰을 소각시키는 함수(burnToken)를 만들어 봅시다.

참고 링크 : https://docs.openzeppelin.com/contracts/5.x/api/token/erc20#ERC20-_burn-address-uint256-

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import { ERC20 } from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MintToken is ERC20 {
    constructor(uint256 _initEther, string memory _name, string memory _symbol) ERC20(_name, _symbol) {
        _mint(msg.sender, _initEther * 10 ** 18);
    }

    function burnToken(uint256 _etherAmount) public {
        _burn(msg.sender, _etherAmount * 10 ** 18);
    }
}
```

먼저 burnToken함수를 보면, 실행부분에 \_burn이 있습니다. \_burn은 뭘까요?

<img
  src="vite/public/images/burn1.png"
  width="718"
  alt="openzeppelin _burn"
  sizes="100vw"
/>

함수를 보시면, 인자로 address 형의 account, uint256형의 value 값을 필요로 합니다.

그런데 우리가 작성한 burnToken()에는 uint256 \_etherAmount(value)만 있고, address형인 account는 받지 않았습니다.

대신, msg.sender라는 예약어를 사용했죠. msg.sender는 함수를 실행시킨 주체입니다.

A가 해당 함수를 실행하면, A가 입력한 \_etherAmount 만큼 A의 토큰을 소각하는 함수입니다.

따라서 burnToken 함수에 인자값으로 account가 없어도 되는 이유입니다.

그럼 내가 다른사람의 토큰을 소각 할 수 있을까요? 아니면 누군가가 나의 토큰을 소각 할 수 있을까요? 확인해봅시다.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import { ERC20 } from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MintToken is ERC20 {
    constructor(uint256 _initEther, string memory _name, string memory _symbol) ERC20(_name, _symbol) {
        _mint(msg.sender, _initEther * 10 ** 18);
    }

    function burnToken(uint256 _etherAmount) public {
        _burn(msg.sender, _etherAmount * 10 ** 18);
    }

    function burnSomeoneToken(address _account, uint _amount) public {
        _burn(_account, _amount * 10 ** 18);
    }
}
```

<img
  src="vite/public/images/burn2.png"
  width="718"
  alt="deploy contract"
  sizes="100vw"
/>

test라는 토큰을 10000개 발행했습니다.

<img
  src="vite/public/images/burn3.png"
  width="718"
  alt="A account balance"
  sizes="100vw"
/>

컨트랙트를 배포한 A계정(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4)에 현재 10000개의 토큰이 있습니다.

그럼 토큰이 없는 B계정(0xAb8483F64d9C6d1EcF9b849Ae677dD3315835cb2)이 A계정의 토큰 100개를 소각해봅시다.

<img
  src="vite/public/images/burn4.png"
  width="718"
  alt="B burn A token"
  sizes="100vw"
/>

위 이미지를 보시면, B의 계정으로 A 토큰 100개가 소각 된 것을 확인 할 수 있습니다.

⚠️ 특정 함수는 관리자만 실행 할 수 있게 작성하는 것은 중요합니다! 컨트랙트가 배포 될 때 한 번만 실행되는 constructor에 관리자를 설정 할 수 있는 코드를 작성하는 것도 방법이겠죠? (이후에 modifier와 같이 배우실 겁니다🙂)
