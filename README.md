# Python에서 XML 파싱하기

[![Bright Data Promo](https://github.com/bright-kr/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.co.kr/)

ElementTree, lxml, SAX 같은 라이브러리를 사용하여 Python에서 XML을 파싱하는 방법을 학습하고 데이터 처리 프로젝트를 강화해 보시기 바랍니다.

- [XML 파일의 핵심 개념](#key-concepts-of-an-xml-file)
- [Python에서 XML을 파싱하는 다양한 방법](#various-ways-to-parse-xml-in-python)
- [ElementTree](#elementtree)
- [lxml](#lxml)
- [minidom](#minidom)
- [SAX Parser](#sax-parser)

## Key Concepts of an XML File

Python에서 XML을 파싱하는 방법을 살펴보기 전에, 먼저 XML Schema Definition (XSD)이 무엇인지와 XML 파일을 구성하는 기본 요소를 이해하는 것이 중요합니다. 이 기초 지식은 파싱 요구사항에 맞는 적절한 Python 라이브러리를 선택하는 데 도움이 됩니다.

[XSD](https://en.wikipedia.org/wiki/XML_Schema_(W3C))는 XML 문서에서 허용되는 구조, 콘텐츠, 데이터 타입을 정의하는 스키마 사양입니다. 이는 검증 규칙 집합 역할을 하며 XML 파일이 일관된 형식을 따르도록 보장합니다.

XML 파일에는 일반적으로 `Namespace`, `root`, `attributes`, `elements`, `text content` 같은 요소가 포함되며, 이들이 함께 구조화된 데이터를 표현합니다.

- **[`Namespace`](https://www.w3schools.com/xml/xml_namespaces.asp)**는 XML 문서의 요소와 attributes를 고유하게 식별합니다. 이름 충돌을 방지하고 서로 다른 XML 데이터セット 간의 상호 운용성을 지원합니다.
- **[`root`](https://en.wikipedia.org/wiki/Root_element)**는 XML 문서의 최상위 요소입니다. XML 구조의 진입점 역할을 하며 다른 모든 요소를 포함합니다.
- **[`attributes`](https://www.w3schools.com/xml/xml_attributes.asp)**는 요소에 대한 추가 컨텍스트를 제공합니다. 요소의 시작 태그 안에 정의되며 이름-값 쌍으로 구성됩니다.
- **[`elements`](https://www.w3schools.com/xml/xml_elements.asp)**는 XML 파일의 핵심 단위로, 실제 데이터 또는 구조 태그를 나타냅니다. 요소는 계층 구조를 만들기 위해 서로 중첩될 수 있습니다.
- **[`text content`](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent)**는 요소의 시작 태그와 종료 태그 사이에 있는 실제 텍스트 데이터입니다. 일반 텍스트, 숫자 값 또는 기타 문자가 포함될 수 있습니다.

예를 들어, [Bright Data sitemap](https://brightdata.co.kr/post-sitemap.xml)은 다음 XML 구조를 따릅니다:

- **`urlset`**은 `root` 요소 역할을 합니다.
- **`<urlset xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd">`**는 `urlset` 요소에 대한 namespace 선언입니다. 이는 스키마 규칙이 `urlset` 및 모든 중첩 요소에 적용됨을 의미합니다.
- **`url`**은 `root` 요소의 직접적인 자식입니다.
- **`loc`**은 `url` 요소 내부의 자식 요소입니다.

이제 XSD와 XML 구조를 더 명확히 이해하셨으므로, 몇 가지 유용한 Python 라이브러리를 사용하여 XML 파일을 파싱하며 이 지식을 활용해 보실 차례입니다.

## Various Ways to Parse XML in Python

Bright Data sitemap을 사용해 보겠습니다. 다음 예시에서는 Python `requests` 라이브러리를 사용해 Bright Data sitemap 콘텐츠를 가져옵니다.

Python requests 라이브러리는 기본 내장 라이브러리가 아니므로 진행하기 전에 설치해야 합니다. 다음 명령으로 설치할 수 있습니다:

```sh
pip install requests
```

## ElementTree

[ElementTree XML API](https://docs.python.org/3/library/xml.etree.elementtree.html)는 Python에서 XML 데이터를 파싱하고 생성하는 간단하고 사용자 친화적인 방법을 제공합니다. Python 표준 라이브러리의 일부이므로 추가 설치가 필요하지 않습니다.

예를 들어, [`findall()`](https://docs.python.org/3/library/xml.etree.elementtree.html#xml.etree.ElementTree.Element.findall) 메서드를 사용하여 root에서 모든 `url` 요소를 가져오고 각 `loc` 요소의 텍스트 콘텐츠를 다음과 같이 출력할 수 있습니다:

```python
import xml.etree.ElementTree as ET
import requests

url = 'https://brightdata.co.kr/post-sitemap.xml'

response = requests.get(url)
if response.status_code == 200:
   
    root = ET.fromstring(response.content)

    for url_element in root.findall('.//{http://www.sitemaps.org/schemas/sitemap/0.9}url'):
        loc_element = url_element.find('{http://www.sitemaps.org/schemas/sitemap/0.9}loc')
        if loc_element is not None:
            print(loc_element.text)
else:
    print("Failed to retrieve XML file from the URL.")

```

sitemap의 모든 URL이 출력에 표시됩니다:

```
https://brightdata.co.kr/case-studies/powerdrop-case-study
https://brightdata.co.kr/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.co.kr/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.co.kr/case-studies/the-seo-transformation
https://brightdata.co.kr/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.co.kr/case-studies/highly-targeted-influencer-marketing
https://brightdata.co.kr/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.co.kr/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.co.kr/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.co.kr/case-studies/data-intensive-analytical-solutions
https://brightdata.co.kr/case-studies/canopy-advantage-solutions
https://brightdata.co.kr/case-studies/seamless-digital-automations
```

ElementTree는 Python에서 간단하고 직관적인 XML 파서로, RSS 피드 읽기 같은 작은 스크립트에 적합합니다. 다만 강력한 스키마 검증이 부족하며 복잡하거나 대규모 XML 파싱에는 적합하지 않을 수 있습니다—그런 경우에는 `lxml` 같은 라이브러리가 더 적합합니다.

## lxml

[lxml](https://lxml.de/)은 Python에서 XML 파일을 파싱하기 위한 빠르고 사용하기 쉬우며 기능이 풍부한 API입니다. `pip`를 사용하여 [`lxml`을 설치](https://lxml.de/installation.html#installation)할 수 있습니다:

```sh
pip install lxml
```

설치 후에는 `find()`, `findall()`, `findtext()`, `get()`, `get_element_by_id()` 같은 [다양한 API](https://lxml.de/apidoc/lxml.html) 메서드를 사용하여 `lxml`로 XML 파일을 파싱할 수 있습니다.

예를 들어, `findall()` 메서드를 사용하여 `url` 요소를 순회하고(이는 `root` 요소의 하위 요소), 각 `url` 요소의 자식 요소인 `loc` 요소를 찾아 다음 코드로 location 텍스트를 출력할 수 있습니다:

```python
from lxml import etree
import requests

url = "https://brightdata.co.kr/post-sitemap.xml"

response = requests.get(url)
if response.status_code == 200:

    root = etree.fromstring(response.content)
    

    for url in root.findall(".//{http://www.sitemaps.org/schemas/sitemap/0.9}url"):
        loc = url.find("{http://www.sitemaps.org/schemas/sitemap/0.9}loc").text.strip()
        print(loc)
else:
    print("Failed to retrieve XML file from the URL.")
```

출력에는 sitemap에서 찾은 모든 URL이 표시됩니다:

```
https://brightdata.co.kr/case-studies/powerdrop-case-study
https://brightdata.co.kr/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.co.kr/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.co.kr/case-studies/the-seo-transformation
https://brightdata.co.kr/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.co.kr/case-studies/highly-targeted-influencer-marketing
https://brightdata.co.kr/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.co.kr/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.co.kr/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.co.kr/case-studies/data-intensive-analytical-solutions
https://brightdata.co.kr/case-studies/canopy-advantage-solutions
https://brightdata.co.kr/case-studies/seamless-digital-automations
```

지금까지 요소를 찾고 그 값을 표시하는 방법을 살펴보았습니다. 다음으로 파싱 전에 스키마에 대해 XML 파일을 검증하는 방법을 살펴보겠습니다. 이 단계는 파일이 XSD에 정의된 구조를 따르는지 확인합니다.

다음은 sitemap의 XSD 예시입니다:

```xml
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           targetNamespace="http://www.sitemaps.org/schemas/sitemap/0.9"
           xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
           elementFormDefault="qualified"
           xmlns:xhtml="http://www.w3.org/1999/xhtml">

  
  <xs:element name="urlset">
    <xs:complexType>
      <xs:sequence>
        <xs:element ref="url" minOccurs="0" maxOccurs="unbounded"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
  
  <xs:element name="url">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="loc" type="xs:anyURI"/>
      </xs:sequence>
    </xs:complexType>
  </xs:element>

</xs:schema>
```

스키마 검증에 이 XSD를 사용하려면, 해당 내용을 수동으로 복사하여 `schema.xsd`라는 파일을 생성하시기 바랍니다.

이제 이 XSD로 XML 파일을 검증합니다:

```python
from lxml import etree

import requests

url = "https://brightdata.co.kr/post-sitemap.xml"

response = requests.get(url)

if response.status_code == 200:

    root = etree.fromstring(response.content)

    try:
        print("Schema Validation:")
        schema_doc = etree.parse("schema.xsd")  
        schema = etree.XMLSchema(schema_doc)  
        schema.assertValid(root)  
        print("XML is valid according to the schema.")
    except etree.DocumentInvalid as e:
        print("XML validation error:", e)
```

이 단계에서는 [`etree.parse()`](https://lxml.de/tutorial.html#the-parse-function) 메서드로 XSD 파일을 파싱한 뒤, 파싱된 콘텐츠에서 XML Schema를 생성합니다. 마지막으로 `assertValid()`를 사용해 해당 스키마에 대해 XML root를 검증합니다. XML이 검증을 통과하면 `XML is valid according to the schema` 같은 메시지가 표시되고, 그렇지 않으면 [`DocumentInvalid`](https://lxml.de/api/lxml.etree.DocumentInvalid-class.html) 예외가 발생합니다.

출력은 다음과 같이 표시됩니다:

```
 Schema Validation:
    XML is valid according to the schema.
```

이제 `xpath` 메서드를 사용해 경로로 요소를 찾아 XML 파일을 읽어 보겠습니다.

`xpath()` 메서드로 요소를 읽으려면 다음 코드를 사용합니다:

```python
from lxml import etree

import requests

url = "https://brightdata.co.kr/post-sitemap.xml"
response = requests.get(url)

if response.status_code == 200:
   
    root = etree.fromstring(response.content)
    
    print("XPath Support:")
    root = etree.fromstring(response.content)

    namespaces = {"ns": "http://www.sitemaps.org/schemas/sitemap/0.9"}
    for url in root.xpath(".//ns:url/ns:loc", namespaces=namespaces):
        print(url.text.strip())

```

이 스니펫에서는 `ns` 프리픽스를 등록하고 이를 namespace URI `http://www.sitemaps.org/schemas/sitemap/0.9`에 연결합니다. 그런 다음 `XPath` 표현식은 이 프리픽스를 사용하여 namespace가 적용된 요소를 대상으로 합니다. 구체적으로 `.//ns:url/ns:loc`는 해당 namespace 내에서 `url` 요소의 자식인 모든 `loc` 요소를 선택합니다.

출력은 다음과 같습니다:

```
XPath Support:

https://brightdata.co.kr/case-studies/powerdrop-case-study
https://brightdata.co.kr/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.co.kr/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.co.kr/case-studies/the-seo-transformation
https://brightdata.co.kr/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.co.kr/case-studies/highly-targeted-influencer-marketing
https://brightdata.co.kr/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.co.kr/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.co.kr/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.co.kr/case-studies/data-intensive-analytical-solutions
https://brightdata.co.kr/case-studies/canopy-advantage-solutions
https://brightdata.co.kr/case-studies/seamless-digital-automations
```

`find()` 및 `findall()` 메서드는 `xpath()`보다 빠른데, 이는 `xpath()`가 모든 결과를 메모리에 로드하기 때문입니다. 더 복잡한 쿼리가 필요하지 않다면 `find()`를 사용하시기 바랍니다.

`lxml`은 XML과 HTML을 파싱하기 위한 강력한 라이브러리로, XPath, 스키마 검증, XSLT 같은 고급 기능을 지원합니다. 고성능 또는 복잡한 작업에 이상적이지만 별도의 설치가 필요합니다.

금융 피드처럼 크거나 복잡한 XML 데이터를 다루는 경우, `lxml`은 효율적인 쿼리, 검증, 변환을 위한 강력한 선택지입니다.

## minidom

[`minidom`](https://docs.python.org/3/library/xml.dom.minidom.html)은 Python 표준 라이브러리에 포함된 간단하고 가벼운 XML 파싱 라이브러리입니다. `lxml`만큼 기능이 풍부하거나 효율적이지는 않지만, XML 데이터를 쉽게 파싱하고 조작할 수 있는 방법을 제공합니다.

다양한 DOM 메서드를 사용하여 요소에 접근할 수 있습니다. 예를 들어, [`getElementsByTagName()` method](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementsByTagName)는 태그 이름으로 요소를 가져올 수 있게 해줍니다.

다음은 `minidom` 라이브러리를 사용하여 XML 파일을 파싱하고 태그 이름으로 요소를 가져오는 예시입니다:

```python
import requests
import xml.dom.minidom

url = "https://brightdata.co.kr/post-sitemap.xml"

response = requests.get(url)
if response.status_code == 200:
    dom = xml.dom.minidom.parseString(response.content)
    
    urlset = dom.getElementsByTagName("urlset")[0]
    for url in urlset.getElementsByTagName("url"):
        loc = url.getElementsByTagName("loc")[0].firstChild.nodeValue.strip()
        print(loc)
else:
    print("Failed to retrieve XML file from the URL.")
```

출력은 다음과 같습니다:

```
https://brightdata.co.kr/case-studies/powerdrop-case-study
https://brightdata.co.kr/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.co.kr/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.co.kr/case-studies/the-seo-transformation
https://brightdata.co.kr/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.co.kr/case-studies/highly-targeted-influencer-marketing
https://brightdata.co.kr/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.co.kr/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.co.kr/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.co.kr/case-studies/data-intensive-analytical-solutions
https://brightdata.co.kr/case-studies/canopy-advantage-solutions
https://brightdata.co.kr/case-studies/seamless-digital-automations
```

`minidom`은 XML 데이터를 DOM 트리로 표현하므로 탐색과 조작이 쉽습니다. 간단한 XML 구조를 읽고, 수정하고, 생성하는 같은 기본 작업에 적합합니다.

프로그램에서 XML 파일로부터 설정을 읽어야 하는 경우, `minidom`을 사용하면 특정 요소에 쉽게 접근할 수 있습니다(예: 자식 노드 또는 attributes 찾기). 예를 들어 `font-size` 노드를 빠르게 가져와 애플리케이션에서 해당 값을 사용할 수 있습니다.

## SAX Parser

[SAX parser](https://docs.python.org/3/library/xml.sax.html)는 문서를 순차적으로 처리하며 시작 태그, 종료 태그, 텍스트 콘텐츠 같은 이벤트를 발생시키는 이벤트 기반 XML 파서입니다. DOM 파서와 달리 SAX는 전체 문서를 메모리에 로드하지 않으므로, 메모리 효율이 중요한 대용량 XML 파일에 이상적입니다.

SAX를 사용하려면 `startElement`, `endElement` 같은 특정 XML 이벤트에 대한 이벤트 핸들러를 정의하며, 문서의 구조와 콘텐츠를 처리하도록 이를 커스터마이즈할 수 있습니다.

다음은 SAX 파서를 사용하여 XML 파일을 처리하고, sitemap에서 URL 정보를 추출하기 위해 이벤트 핸들러를 정의하는 예시입니다:

```python
import requests
import xml.sax.handler
from io import BytesIO

class MyContentHandler(xml.sax.handler.ContentHandler):
    def __init__(self):
        self.in_url = False
        self.in_loc = False
        self.url = ""

    def startElement(self, name, attrs):
        if name == "url":
            self.in_url = True
        elif name == "loc" and self.in_url:
            self.in_loc = True

    def characters(self, content):
        if self.in_loc:
            self.url += content

    def endElement(self, name):
        if name == "url":
            print(self.url.strip())
            self.url = ""
            self.in_url = False
        elif name == "loc":
            self.in_loc = False

url = "https://brightdata.co.kr/post-sitemap.xml"

response = requests.get(url)
if response.status_code == 200:

    xml_content = BytesIO(response.content)
    
    content_handler = MyContentHandler()
    parser = xml.sax.make_parser()
    parser.setContentHandler(content_handler)
    parser.parse(xml_content)
else:
    print("Failed to retrieve XML file from the URL.")
```

출력은 다음과 같습니다:

```
https://brightdata.co.kr/case-studies/powerdrop-case-study
https://brightdata.co.kr/case-studies/addressing-brand-protection-from-every-angle
https://brightdata.co.kr/case-studies/taking-control-of-the-digital-shelf-with-public-online-data
https://brightdata.co.kr/case-studies/the-seo-transformation
https://brightdata.co.kr/case-studies/data-driven-automated-e-commerce-tools
https://brightdata.co.kr/case-studies/highly-targeted-influencer-marketing
https://brightdata.co.kr/case-studies/data-driven-products-for-smarter-shopping-solutions
https://brightdata.co.kr/case-studies/workplace-diversity-facilitated-by-online-data
https://brightdata.co.kr/case-studies/alternative-travel-solutions-enabled-by-online-data-railofy
https://brightdata.co.kr/case-studies/data-intensive-analytical-solutions
https://brightdata.co.kr/case-studies/canopy-advantage-solutions
https://brightdata.co.kr/case-studies/seamless-digital-automations
```

전체 파일을 메모리에 로드하는 다른 파서와 달리, SAX는 파일을 점진적으로 처리하므로 메모리를 절약하고 성능을 개선합니다. 하지만 각 데이터 구간을 처리하기 위한 코드가 더 많이 필요하고, 나중 분석을 위해 데이터의 특정 부분을 다시 방문하는 것은 허용하지 않습니다.

SAX는 대용량 XML 파일(예: 로그 파일)을 효율적으로 스캔하여 특정 정보(예: 오류 메시지)를 추출하는 데 이상적입니다. 그러나 분석에서 서로 다른 데이터 구간 간의 관계를 탐색해야 한다면, SAX는 최선의 선택이 아닐 수 있습니다.

## Conclusion

Python은 XML 파싱을 단순화하는 다재다능한 라이브러리를 제공합니다. 그러나 requests 라이브러리로 온라인 파일에 접근할 때는 쿼터 및 스로틀링 문제에 직면할 수 있습니다. [Bright Data](https://brightdata.co.kr/)는 이러한 제한을 우회하는 데 도움이 되는 신뢰할 수 있는 프록시 솔루션을 제공합니다. 

스크레이핑과 파싱을 건너뛰고 싶으시다면, 무료로 제공되는 [dataset marketplace](https://brightdata.co.kr/products/datasets)를 확인해 보시기 바랍니다!