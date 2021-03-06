转载自http://blog.csdn.net/cjr15233661143/article/details/7842023


在SQL Server数据库中提供了两种主要机制来强制使用业务规则和数据完整性，它们是SQL Server约束和触发器。触发器其实就是一个特殊类型的存储过程，可以在执行某个操作时自动触发。触发器与约束都可以实现数据的一致性。那么他们在使用的过程中，有哪些差异呢?
        约束和触发器在特殊情况下各有优势。
    约束主要被用于强制数据的完整性，约束也能提供比触发器更好的性能。然而在所能完成的操作，以及完成工作时所能使用约束是有限制的。触发器则常被用于验证业务规则，或是更复杂的数据验证，然而可以对数据的其他地方的数据完成更深入的更新，约束只能对其所在表中的数据，或是在设计时输入的特定数据进行验证。这同触发器形成对比，触发器可以跨越数据库甚至服务器，可以对任何在设计时设置的数据，或从任何表上的其他行为所收集的数据进行检查。如果所需的访问权限被给予所有包含的对象，就可以使用触发器的这些功能。
    简单的来说，触发器可以实现约束的一切功能。但是在考虑数据一致性问题的时候，首先要考虑通过约束来实现。如果约束无法完成的功能，则再通过触发器来解决。两者从功能上来说，他们的关系如下图所示：
  
![这里写图片描述](http://my.csdn.net/uploads/201208/08/1344394551_2142.jpg)
    触发器可以包含使用SQL代码的复杂处理逻辑。如果单从功能上来说，触发器可以实现约束的所有功能。但是由于其自身的种种缺陷，其往往不是实现数据一致性等特定功能的首选解决方案。总的来说 只有在约束无法实现特定功能的情况下，才考虑通过触发器来完成。这只是在处理约束与触发器操作过程中的一个基本原则。对于它们两个具体的差异在下面也进行了比较详细的阐述。
 
区别一：错误信息的管理上。
    当违反系统的SQL Server约束规则时，需要向用户返回一定的错误信息，方便用户进行排错。约束与触发器在遇到问题时都可以返回给用户一定的错误信息。但是，约束只能够通过标准化的系统错误信息来传递错误消息。如果应用程序需要使用自定义消息和较为复杂的错误处理机制，则必须要使用触发器才能够完成。如现在数据库中有一张产品信息表。为了保证产品的唯一性，要求产品的编号必须唯一。如果用户输入的产品编号跟企业现有的产品编号有重复的话，这条产品信息就不能够被保存。从技术上来说，约束与触发器都可以实现这个需求。但是，当违反这个唯一性规则时，他们提供的错误信息是不同的。
如利用约束来实现这个唯一性控制，那么当用户输入重复的编号时，则系统会提示违反了唯一性规则，不允许保存。但是光凭这条消息的话，可能用户还不能够马上了解是怎么回事情。有时候程序员希望能够返回更加具体的信息。这时候就需要用到触发器了。因为只有触发器可以返回数据库管理员自定义的错误信息;而且还可以实现比较复杂的逻辑控制。而约束只能够范围系统定义的标准错误信息。
 
区别二：性能上的差异分析。
     相信大家看了下面的例子肯定会对它们在性能上存在的差异有进一步的了解。
    如现在有两张表，分别为销售订单头与销售订单行。在销售订单中有一个订单ID，它是这张表的主键，也是销售订单行表的外键。现在如果更改了销售订单头表的主键的值，那么必须要保证销售订单行表中订单ID的值也随之更改。否则的话，销售订单头表与销售订单行表就无法对应起来。此时触发器与约束都可以实现类似的功能。触发器可以将销售订单头ID的更改通过级联更新的功能传播给数据库中其他相关的表，实现级联更新。约束也可以实现类似的功能。而且通常情况下，通过级联引用完整性约束可以更有效的执行这个级联更新。如当上面这个更改发生后，触发器可以禁止或回滚违反引用完整性的更改，从而取消所尝试的数据修改。当更改外键且新值与其主键不匹配时，这个的触发器将生效。但是，数据库中有一个现成的解决方案，即FOREIGN KEY 约束通常用于此目的。如果触发器表上存在约束，则在 INSTEAD OF 触发器执行后但在 AFTER 触发器执行前检查这些约束。如果违反了约束，则回滚 INSTEAD OF 触发器操作并且不执行 AFTER 触发器。
    在网上查了一下遇到这种情况后，往往有两种处理方式。一是如果要更改的主键在其他表中已经存在的话，那么就不允许其进行更改，系统会拒绝保存或者回滚用户的更改操作。二是如果要更改的主键信息在其他表中已经存在相关的记录，而数据库管理员又允许其更改的话，为了保证数据的一致性，就要出发级联更新的功能。让数据库系统在更改主键的同时自动更新其他表中的相关信息。无论采取哪种方式，从性能上来说，约束的执行性能都要高一点。而且系统本身就提供了一些约束规则，如级联引用完整性约束的等等。故也省去了管理员写触发器代码的工作量。不过有一点值得说明的是，虽然约束的执行性能比较高，但是其向用户提供的错误信息确实非常有限的。如上面第一点所说，系统只提供了一些标准的错误信息。如果管理员需要向用户提供比较详细的错误信息，则需要通过触发器的自定义错误信息来实现。故在用户的友好性与数据库的执行性能之间，数据库管理员需要做出一个均衡。
 
区别三：管理维护的工作量。
    由于约束基本上都是数据库现成的解决方案。无论是索引约束还是外键约束，还是check约束。往往在数据库系统中已经有了现成的解决方案。数据库管理员通过直接引用这些解决方案即可以实现特定的功能，而不用再费力的编写触发器来实现。如要实现表中某个字段的唯一性约束，则只需要直接在这个字段上启用unique约束即可。从而省去了编写触发器代码的时间。所以通常来说，触发器的维护工作量要比约束来的多。因为触发器中系统没有现成的可以引用，而都需要数据库管理员通过实际清理来进行编写。如果不熟悉编制的话，还很容易引起不必要的错误。为此，如果单从这个工作量来考虑的话，那么数据库管理员肯定喜欢采用这个约束，而不喜欢采用触发器。
 
建议：
    如果约束能够实现特定的功能，则数据库最好能够采用约束而不是触发器。因为约束能够提供比较高的执行性能，而且数据库管理员维护的工作量也会小得多。当然使用约束的前提是假设这些约束的功能能够满足应用程序的功能需求。如果系统中现成的约束无法满足企业用户的需求，如功能无法满足或者提供的错误信息不够等情况，此时数据库管理员就需要通过触发器来完成。不过数据库管理员在编写触发器的时候，仍然可以借鉴相关约束的实现方式。而不用从零开始，来重新设计触发器。另外触发器可以防止一些恶意或错误的记录插入、删除以及更新操作，并强制执行比CHECK约束定义的限制更为复杂的其他限制。其还可以提供比CHECK约束更复杂一点的功能。如触发器可以引用其他表中的列。可见触发器与约束各有各的特点。要从执行性能、维护工作量、实现的功能、用户友好性等多个方面出发，选择合适的处理方式。