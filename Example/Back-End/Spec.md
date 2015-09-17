# Spec
`取得文章清單`


```

  describe('find one and all', (done) => {


    it("index all post", (done) => {

      request.get("/rest/post/")
      .expect(200)
      .end((error, res) => {
        let posts = res.body.posts;

        posts.should.be.Array;
        posts[0].id.should.greaterThan(0);

        done(error);
      });

    });

    it("find single post", (done) => {

      request.get("/rest/post/1")
      .expect(200)
      .end((error, res) => {

        models.User.findById('1').then((result) =>{
          (result !== null).should.true
          done(error);
        });
      });
    });
  });

```
